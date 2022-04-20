<template >
    <div class="card text-center" style="min-width: 80rem;">
    <div class="card-header d-flex justify-content-center"> 
      <h3 style="margin-right: 20px">Se connecter</h3>
      <svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" fill="currentColor" class="bi bi-door-closed" viewBox="0 0 16 16">
        <path d="M3 2a1 1 0 0 1 1-1h8a1 1 0 0 1 1 1v13h1.5a.5.5 0 0 1 0 1h-13a.5.5 0 0 1 0-1H3V2zm1 13h8V2H4v13z"/>
        <path d="M9 9a1 1 0 1 0 2 0 1 1 0 0 0-2 0z"/>
      </svg>
    </div>
      <div class="card-body">
        <input class="form-control" v-model="email" name="email" type="email" placeholder="email"/>
        <input class="form-control" v-model="password" name="password" type="password" placeholder="password"/>
        <div><span id = "connexion" style="color:red;"> </span></div>
        <input class="btn btn-success" type="submit" value="Connexion" @click="(e) => {connexion(e)}"/>
      </div>
    </div>
</template>

<script>

export default {
    data (){
        return {
            email : "",
            password : "",
            users:[]
        }
    },
    methods:{
        connexion(e){
          e.preventDefault();
          fetch("http://localhost:3004/utilisateurs", {
            method: "GET", 
            headers : {"content-type": "application/json"} , 
          })
          .then(reponse => reponse.json())
          .then(data => {
            this.users = data;
            var check = false;
            data.map(user => {
              if(user.email == this.email && user.password == this.password){
                check = true;
                const commit = {
                  pseudo: user.pseudo,
                  email: user.email
                }
                this.$store.commit({type: 'setUserConnected' , user : commit})
                if (localStorage.getItem("loggedIn") === "true")  {
                  this.$forceUpdate();
                  this.$emit('update_loggedIn')
                  this.$router.push('/')
                } 
              }
            })
            if(!check) document.querySelector('#connexion').innerHTML = "L'utilisateur n'existe pas, veuillez vérifier les informations saisies"
          })
          
        }
    }
}
</script>

<style>
@media (min-width: 1024px) {
  .connexion {
    min-height: 100vh;
    margin-left:5em;
    background-color:#FBF9F8;
    width:80%;
    border: 0.6px solid #C8C5C4;
  }
}
.inputs{
  margin-right:3em;
  margin-left:3em;
}
</style>
