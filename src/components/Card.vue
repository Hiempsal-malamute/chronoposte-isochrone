<template>
      <div class="card-result" @click="onClick(content)" 
                               @mouseover="onMouseOver(content)"
                               @mouseout="onMouseLeave">
        <div class="content">
          <h5>{{ content.libelle_du_site}}</h5>
          <span>{{ content.adresse }}</span><br>
          <span>{{ content.code_postal }} {{ content.localite }}</span><br>
        </div>
      </div>
</template>


<script>
import L from 'leaflet'
import "leaflet/dist/leaflet.css";



export default {
  name: 'Card',
  props: {
    content:Object
  },
  computed: {
    map() {
      return this.$store.state.map
    },
    hoverLayer() {
      return new L.layerGroup({}).addTo(this.map)
    },
    clickedLayer() {
      return new L.layerGroup({}).addTo(this.map)
    }
  },
  methods: {
    onClick(e) {
      
      let coords = [e.latitude, e.longitude];
      this.clickedLayer.clearLayers();

      let marker = new L.circleMarker(coords, {
        radius:10,
        fillOpacity:0,
        color:'red',
        opacity:1,
        weight:3
      })
      marker.addTo(this.clickedLayer);
      this.map.flyTo([marker._latlng.lat,marker._latlng.lng])
      
    },
    onMouseOver(e) {
      let coords = [e.latitude, e.longitude];
      new L.marker(coords).bindTooltip(e.libelle_du_site).openTooltip().addTo(this.hoverLayer)
    },
    onMouseLeave() {
      this.hoverLayer.clearLayers()
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.card-result {
  text-align: left;
  font-size: .85em;
  display: block;
  cursor: pointer;
  width: 100%;
  border-bottom: solid 0.5px rgb(172, 169, 169); 
  background-color: white;
}


.card-result:hover {
    background-color: rgb(230,230,230);
    border-left: solid 3px blue;
}

.content {
  padding:5px
}

a {
  color: #42b983;
}


</style>
