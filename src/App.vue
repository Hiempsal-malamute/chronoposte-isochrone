<template>
  <div id="nav" class="nav">
    <router-link to="/">Accueil</router-link> |
    <router-link to="/map-view">Carte</router-link> |
  </div>
  <router-view/>
</template>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0;
}

#nav {
  padding: 10px;
  background: linear-gradient(to left, rgb(121, 93, 173),rgb(60, 65, 133));
}

#nav a {
  font-weight: bold;
  color: white;
}

#nav a.router-link-exact-active {
  color: #42b983;
}

@import'~bootstrap/dist/css/bootstrap.css'
</style>
