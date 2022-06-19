<template>
    <div id="map"></div>
</template>


<script>

import * as turf from '@turf/turf'
import postes from '../db/laposte_poincont2.json'
import L from 'leaflet'
import "leaflet/dist/leaflet.css";

delete L.Icon.Default.prototype._getIconUrl

L.Icon.Default.mergeOptions({
  iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
  iconUrl: require('leaflet/dist/images/marker-icon.png'),
  shadowUrl: require('leaflet/dist/images/marker-shadow.png')
})



export default {
  name: 'MapComponent',
  props: {
    text: String
  },
  data() {
    return {
      road2Url:'https://itineraire.ign.fr/simple/1.0.0/isochrone?'
    }
  },
  watch: {
    run() {

      let latlng = this.$store.state.resultAddress.resultCoords;
      let transportMode = this.$store.state.transportMode;
      let duration = this.$store.state.duration;
      this.getIsochrone(latlng[0],latlng[1],transportMode,duration);

      this.$store.state.run = '';
      this.$store.state.map = this.map;
    }
  },
  computed: {
    map() {
      // let extent = this.$route.query['@'].split(',');
      // console.log(extent);
      let map = L.map('map').setView([46.413220, 1.219482],6);
      L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png').addTo(map);
      return map
    },
    postes() {
      let i = 0;
      return postes.filter(e => {
        i++;
        e.fields.id = String(i);
        return e.fields.latlong != undefined
      });

    },
    run() {
      return this.$store.state.run
    },
    computing() {
      return this.$store.state.computing
    },
    isochronesLayer() {
      return new L.layerGroup({}).addTo(this.map)
    },
    pointsLayer() {
      return new L.layerGroup({}).addTo(this.map)
    }
  },
  mounted() {
    this.map.on('moveend',this.setMapExtent);
  },
  methods: {
    setMapExtent() {
      let map = this.map;
      let center = map.getCenter();
      let lat = center.lat.toFixed(4);
      let lng = center.lng.toFixed(4);
      let z = map.getZoom();
      this.$router.push({path:'map-view', query: {'@':`${lat},${lng},${z}`}});
      // console.log(this.$route.query['@'].split(','));
    },
    // calcul isochrones
    getIsochrone(lat,lng,mode,val) {
      let map = this.map;

      let req = this.road2Url + 
      "point=" + lng + "," + lat +
      "&resource=" + "bdtopo-pgr" +
      "&costType=" + "time" +
      "&profile=" + mode +
      "&costValue=" + val + 
      "&timeUnit=minute";

      // spinner.style.display = "block";

      L.marker([lat, lng]).addTo(map);

      this.$store.state.computing = true;
      console.log(this.computing);

      fetch(req)
      .then(res => res.json())
      .then(res => {
        this.isochronesLayer.clearLayers()
        let polygon = new L.GeoJSON(res.geometry, {
            style:{
                color:this.getColor(val),
                fillColor:this.getColor(val),
                weight:3,
                opacity:1,
                fillOpacity:.25
            }
        })
        polygon.addTo(this.isochronesLayer);
        map.flyToBounds(polygon.getBounds(), { duration:1 });
        this.getResults(polygon);
        // spinner.style.display = "none";
        this.$store.state.computing= false;
        return polygon
      }).catch(error => console.log(error));
    },
    getResults(polygon) {
      this.pointsLayer.clearLayers()
      // let map = this.map;
      let postes = this.postes;

      let points = [];
      postes.forEach(e => {
        e = e.fields;
        let point = turf.point([e.longitude,e.latitude], e);
        points.push(point)
      });
      points = turf.featureCollection(points);
     
      let searchWithin = polygon.toGeoJSON();

      let ptsWithin = turf.pointsWithinPolygon(points, searchWithin, {i:"isochrone"});
      
      let geoPts = new L.GeoJSON(ptsWithin, {      
        pointToLayer:(feature,latlng) => {
          console.log(feature);
            return L.circleMarker(latlng,{
                fillColor:'yellow',
                radius:7,
                fillOpacity:1,
                color:'black',
                weight:1
            }).bindTooltip(feature.properties.libelle_du_site).openTooltip()
        }
      })
      geoPts.addTo(this.pointsLayer);
            // remplissage liste résultats
      let resultList = [];
      ptsWithin.features.forEach(e => {
        resultList.push(e.properties)
      });
      this.$store.state.resultsList = resultList;

    },
    getColor(val) {
      if(val == 10) {return "green"}
      else if(val == 20) {return "orange"} 
      else if(val == 30) {return "red"} 
    }
    // getMapExtent() {
    //   let extent = this.$route.query['@'].split(',');
    //   console.log(extent);
    // }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

#map {
  display: flex;
  flex-grow: 1;
  width: 100%;
  height: 100%;
}

</style>
