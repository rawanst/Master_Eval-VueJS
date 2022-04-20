<script setup>
import { onBeforeUpdate } from '@vue/runtime-core'
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <div class="d-flex flex-row">
      <h1>MyNetWork </h1>
      <div class="mt-1 ml-5">
        <svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" fill="currentColor" class="bi bi-facebook" viewBox="0 0 16 16">
          <path d="M16 8.049c0-4.446-3.582-8.05-8-8.05C3.58 0-.002 3.603-.002 8.05c0 4.017 2.926 7.347 6.75 7.951v-5.625h-2.03V8.05H6.75V6.275c0-2.017 1.195-3.131 3.022-3.131.876 0 1.791.157 1.791.157v1.98h-1.009c-.993 0-1.303.621-1.303 1.258v1.51h2.218l-.354 2.326H9.25V16c3.824-.604 6.75-3.934 6.75-7.951z"/>
        </svg>
      </div>
    </div>
  <div class="d-flex flex-row">
    <div class="p-2">

      <div class="card" style="width: 13rem;">
        <ul class="list-group list-group-flush">
          <li class="list-group-item d-flex flex-row">
            <svg style="" xmlns="http://www.w3.org/2000/svg" width="22" height="22" fill="currentColor" class="bi bi-house-door mt-2" viewBox="0 0 16 16">
              <path d="M8.354 1.146a.5.5 0 0 0-.708 0l-6 6A.5.5 0 0 0 1.5 7.5v7a.5.5 0 0 0 .5.5h4.5a.5.5 0 0 0 .5-.5v-4h2v4a.5.5 0 0 0 .5.5H14a.5.5 0 0 0 .5-.5v-7a.5.5 0 0 0-.146-.354L13 5.793V2.5a.5.5 0 0 0-.5-.5h-1a.5.5 0 0 0-.5.5v1.293L8.354 1.146zM2.5 14V7.707l5.5-5.5 5.5 5.5V14H10v-4a.5.5 0 0 0-.5-.5h-3a.5.5 0 0 0-.5.5v4H2.5z"/>
            </svg>
            <RouterLink class="nav-link" to="/">
              Fils d'actualités
            </RouterLink>
          </li>
          <li v-if="loggedIn === false" class="list-group-item d-flex flex-row">
            <svg xmlns="http://www.w3.org/2000/svg" width="22" height="22" fill="currentColor" class="bi bi-person-circle mt-2" viewBox="0 0 16 16">
              <path d="M11 6a3 3 0 1 1-6 0 3 3 0 0 1 6 0z"/>
              <path fill-rule="evenodd" d="M0 8a8 8 0 1 1 16 0A8 8 0 0 1 0 8zm8-7a7 7 0 0 0-5.468 11.37C3.242 11.226 4.805 10 8 10s4.757 1.225 5.468 2.37A7 7 0 0 0 8 1z"/>
            </svg>
            <RouterLink class="nav-link" to="/inscription" >
              Créer un profil
            </RouterLink> 
          </li>
          <li v-if="loggedIn === false" class="list-group-item d-flex flex-row">
            <svg xmlns="http://www.w3.org/2000/svg" width="22" height="22" fill="currentColor" class="bi bi-door-closed mt-2" viewBox="0 0 16 16">
              <path d="M3 2a1 1 0 0 1 1-1h8a1 1 0 0 1 1 1v13h1.5a.5.5 0 0 1 0 1h-13a.5.5 0 0 1 0-1H3V2zm1 13h8V2H4v13z"/>
              <path d="M9 9a1 1 0 1 0 2 0 1 1 0 0 0-2 0z"/>
            </svg>
            <RouterLink class="nav-link" to="/connexion" >
              Se connecter
            </RouterLink>
          </li>
          <li v-if="loggedIn === true" class="list-group-item d-flex flex-row">
            <svg xmlns="http://www.w3.org/2000/svg" width="22" height="22" fill="currentColor" class="bi bi-door-open mt-2" viewBox="0 0 16 16">
              <path d="M8.5 10c-.276 0-.5-.448-.5-1s.224-1 .5-1 .5.448.5 1-.224 1-.5 1z"/>
              <path d="M10.828.122A.5.5 0 0 1 11 .5V1h.5A1.5 1.5 0 0 1 13 2.5V15h1.5a.5.5 0 0 1 0 1h-13a.5.5 0 0 1 0-1H3V1.5a.5.5 0 0 1 .43-.495l7-1a.5.5 0 0 1 .398.117zM11.5 2H11v13h1V2.5a.5.5 0 0 0-.5-.5zM4 1.934V15h6V1.077l-6 .857z"/>
            </svg>
            <button class="nav-link" @click="(e) => {deconnexion(e)}" style="background-color:transparent;border:none;">
            Se deconnecter
            </button>
          </li>
        </ul>
      </div>
    </div>
    
    <div class="p-2">
      <RouterView @statusConnexion="reloadPage" @update_loggedIn="updateparent"/>
    </div> 
  </div>
</template>

<script>
  
export default {
  data () {
      return {
        loggedIn: false
      }
    },
    created() {
        if (localStorage.getItem("loggedIn") === "true")  {
          this.loggedIn = true
          
        }
    },
  methods:{
    deconnexion(e){
      this.loggedIn = false
      e.preventDefault()
      this.$store.commit({type: 'setUserDeconnected'})
      this.$router.push({ path: '/connexion' })
      this.reloadPage()
    },
    reloadPage(){
      this.$forceUpdate();
    },
    updateparent() {
      if(localStorage.getItem("loggedIn") === "true")
        this.loggedIn = true
        else this.loggedIn = false
    }
  },
  beforeCreate: function(){
    var users = []
    fetch("http://localhost:3004/utilisateurs")
    .then(reponse => reponse.json())
    .then(data => {
        users = data ; 
    })
    fetch("http://localhost:3004/articles")
    .then(reponse => reponse.json())
    .then(data => {
      this.$store.articles = data ; 
      this.$store.articles.forEach(article => {
        users.forEach(user => {
          if(article.pseudo == user.pseudo) article.urlImgProfil = user.urlImgProfil
        });
      })
    })
  },
  mounted: function(){
    if (localStorage.getItem("loggedIn") === "true")  {
      this.loggedIn = true
    }else{
      this.loggedIn = false
      this.$forceUpdate()
    } 
  },
}
</script>

<style>
  input{
    margin-top:0.5em;
  }
</style>
