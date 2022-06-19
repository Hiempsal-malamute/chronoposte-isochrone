<template>
    <div class="container-fluid">
      <div id="loading" v-if="computing">
          <div class="row">
              <div class="spinner-border" role="status">
                  <!-- <p class="sr-only">Loading...</p> -->
              </div>
          </div>
          <div class="row">
              <p>Calcul en cours ...</p>
          </div>
      </div>
      <div class="row no-gutters h-100">
          <!-- <div class="col-sm-1">
            <Sidebar id="sidebar"/>
          </div> -->
          <div class="col-sm-2" id="sidepanel-container">
            <SidePanel/>
          </div>
          <div class="col-sm-8 p-0">
            <MapComponent text="This is the map" ref="map"/>
          </div>
          <div class="col-sm-2" id="results-container">
            <h3>Résultats</h3>
            <div class="results-list">
              <Card v-for="(result,i) in resultList" 
                    :key="i" 
                    :content="result"/>
            </div>
          </div>
        </div> 
    </div>
</template>

<script>
// @ is an alias to /src
import MapComponent from '@/components/MapComponent.vue';
// import Sidebar from '@/components/Sidebar.vue'
import SidePanel from '@/components/SidePanel.vue';
import Card from '@/components/Card.vue';

export default {
  name: 'MapView',
  components: {
    MapComponent,
    // Sidebar,
    SidePanel,
    Card
  },
  computed: {
    resultList() {
      return this.$store.state.resultsList
    },
    computing() {
      return this.$store.state.computing
    },
  },
  // methods: {
  //   onClick(e) {
  //     let map = this.$store.state.map;
  //     console.log(map);
  //     console.log(Array.from(e.latlong));
  //     map.flyTo(Array.from(e.latlong), {duration:1})

  //   }
  // },
}
</script>

<style scoped>

.container-fluid {
  overflow: hidden;
}

#sidepanel-container{
  z-index: 10000;
  background: rgb(240,240,240);
	box-shadow: 0 10px 10px rgba(0,0,0,.08), 0 4px 8px rgba(0,0,0,.2);
}

#results-container {
  padding:0;
  text-align: left;
  z-index: 10000;
	box-shadow: 0 10px 10px rgba(0,0,0,.08), 0 4px 8px rgba(0,0,0,.2);
  height: calc(100vh - 50px);
  overflow-y: scroll;
}


h3 {
  padding:10px;
}


html, body {
  font-size:.7em !important;
  width: 100%;
}

#loading {
  position: absolute;
  justify-content: center;
  align-items: center;
  display: flex;
  left:0;
  top:0;
  width: 100%;
  height: 100vh;
  /* height: calc(100vh - 50px); */
  background: rgba(0,0,0,.7);
  z-index: 20000;
  color:white;
  flex-direction: column;
  overflow-x: hidden;
}


</style>
