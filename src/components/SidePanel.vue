<template>
      <div id="sidepanel">
        <h3>Paramètres</h3>
        Recherche
        <SearchBar @searchResult="getSearchResult"/>
        <div class="transport">
            <label for="form-select">Mode de déplacement</label>
            <select class="form-select" 
                    aria-label="Default select example" 
                    id="select-transport"
                    :onchange="onChangeTransport">
                <option  value="car">Voiture</option>
                <option selected value="pedestrian">À pied</option>
              </select>
        </div><br>
        <div class="time">
            <label for="form-select">Temps de trajet</label>
            <select class="form-select" 
                    aria-label="Default select example" 
                    id="select-time"
                    :onchange="onChangeTime">
                <option selected value="10">10 minutes</option>
                <option value="20">20 minutes</option>
                <option value="30">30 minutes</option>
                <option value="40">40 minutes</option>
              </select>
        </div><br>
        <button type="button" class="btn btn-primary w-100" @click="onClick">Lancer la recherche</button>
        <!-- <button type="button" class="btn btn-primary w-100" @click="reset">Réinitialiser</button> -->
      </div>
</template>


<script>

import "leaflet/dist/leaflet.css";
import SearchBar from '@/components/Search.vue'


export default {
  name: 'SidePanel',
  components: {
    SearchBar:SearchBar
  },
  data() {
    return {
      resultAddress:'',
      transportMode:'',
      duration:'',
    }
  },
  methods: {
    getSearchResult(e) {
      this.resultAddress = e;
      this.$store.state.resultAddress = e;
      console.log(this.$store.state);
    },
    onChangeTransport(e) {
      this.transportMode = e.target.value;
      this.$store.state.transportMode = e.target.value;
    },
    onChangeTime(e) {
      this.duration = e.target.value;
      this.$store.state.duration = e.target.value;
    },
    onClick() {
      this.$store.state.run = true;
    },
    reset() {
      let map = this.$store.state.map;
      map.eachLayer(function (layer) {
          if(layer._url != 'https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png') {
            map.removeLayer(layer);
          }
      });
      map.setView([46.413220, 1.219482],6);
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#sidepanel {
  text-align: left;
  height: calc(100vh - 50px);
}

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


</style>
