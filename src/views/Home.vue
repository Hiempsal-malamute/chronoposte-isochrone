<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <p>
      Cette application est une démo requêtant l'API de calcul d'isochrones et isodistances de l'IGN, combinée à l'utilisation des frameworks VueJS, Leaflet et TurfJS et de l'API adresse exploitant la Base adresse nationale (Étalab / IGN). 
      <br>Il ne s'agit pas d'un produit fini ; en outre, de nombreux bugs subsistent et sa vocation première étant de tester de l'API, elle n'a pas vocation à connaitre de développement plus approfondi dans l'immédiat
    </p>
    <p>
      L'API est utilisée pour rendre compte de l'accessibilité des bureaux de postes en France en fonction 
      <ul>
        <li>
          1) d'un critère de temps théorique de déplacement  
        </li>
        <li>
          2) d'un mode de déplacement (pédestre ou voiture)  
        </li>
      </ul>
      La restitution se fait sur une carte, croisant les isochrones calculées grâce à l'API de l'IGN et la localisation des bureaux de postes présents dans les zones de desserte calculées.
      <br>
      Le jeu de données test est issu du 
      <a href="https://datanova.laposte.fr/explore/dataset/laposte_poincont2/information/?disjunctive.caracteristique_du_site&disjunctive.code_postal&disjunctive.localite&disjunctive.code_insee&disjunctive.precision_du_geocodage" target="_blank">
        portail opendata du groupe La poste 
      </a>
    </p>
    <p>
      <router-link to="/map-view">
        Accéder à la démo
      </router-link>
    </p>
    <p>
      Plus d'informations à propos de l'API sur
      <a href="https://geoservices.ign.fr/documentation/services/api-et-services-ogc/isochrones" target="_blank">
        le portail des géoservices de l'IGN
      </a>
    </p>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: 'Home',
}
</script>

<style>
  .home {
    display: flex;
    justify-content: center;
    vertical-align: middle;
    align-items: center;
    flex-direction: column;
    text-align: center;
    padding:50px;
  }

  a.router-link {
    color: #42b983 !important;
  }

  ul, li {
    list-style-type: none;
  }
</style>