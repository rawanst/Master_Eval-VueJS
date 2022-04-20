<template>
    <div class="card text-center" style="min-width: 80rem;">
      <div class="card-header d-flex justify-content-center"> 
        <h3 style="margin-right: 20px">Créer un nouveau profil</h3>
        <svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" fill="currentColor" class="bi bi-person-circle" viewBox="0 0 16 16">
            <path d="M11 6a3 3 0 1 1-6 0 3 3 0 0 1 6 0z"/>
            <path fill-rule="evenodd" d="M0 8a8 8 0 1 1 16 0A8 8 0 0 1 0 8zm8-7a7 7 0 0 0-5.468 11.37C3.242 11.226 4.805 10 8 10s4.757 1.225 5.468 2.37A7 7 0 0 0 8 1z"/>
        </svg>
      </div>
      <div class="card-body">
        <input class="form-control" v-model="pseudo" name="psoeudo" type="text" placeholder="pseudo" required/>
        <input class="form-control" v-model="email" name="email" type="email" placeholder="email" required/>
        <input class="form-control" v-model="password" name="password"  type="password" placeholder="password" id="password" required/>
        <input class="form-control" v-model="confirm_password" name="confirm_password" type="password" placeholder="confirmer password" id="confirm_password" required/>
        <span id = "message" style="color:red"> </span>
        <input class="form-control" v-model="urlImgProfil" name="urmImage" type="text" placeholder="url de votre image - taille conseillée 1000x200px" required/>

        <input class="btn btn-success" type="submit" value="Créer un nouveau profil" @click="(e) => {inscrire(e)}"/>
        <div><span id = "inscription"> </span></div>
      </div>
    </div>
</template>
<script>

export default {
    data (){
        return {
            pseudo : "",
            email : "",
            password : "",
            confirm_password : "",
            urlImgProfil : ""
        }
    },
    methods:{
        inscrire(e){
            e.preventDefault();
            if(this.password != this.confirm_password){
                document.querySelector('#message').innerHTML="Les mots de passe ne sont pas identiques !";
            }else{
                const user = {
                    pseudo : this.pseudo,
                    email : this.email,
                    password : this.password,
                    urlImgProfil : this.urlImgProfil
                }
                fetch("http://localhost:3004/utilisateurs", {
                    method: "post", 
                    headers : {"content-type": "application/json"} , 
                    body : JSON.stringify(user)
                })
                .then(reponse => reponse.json())
                .then(data => {
                })
                this.pseudo =""
                this.email =""
                this.password =""
                this.confirm_password =""
                this.urlImgProfil =""
                document.querySelector('#inscription').innerHTML="L'utilisateur a été bien inscrit !";
                this.$router.push('/connexion')
            }
        }
    },
    computed:{
        
    },
    mounted:function(){
        fetch("http://localhost:3004/articles")
            .then(reponse => reponse.json())
            .then(data => {
                
                this.articles = data ; 
        })
        fetch("http://localhost:3004/utilisateurs")
            .then(reponse => reponse.json())
            .then(data => {
                this.users = data ; 
        })
    }
}
</script>
<style>
    @media (min-width: 1024px) {
        .inscription {
            min-height: 100vh;
            margin-left:5em;
            background-color:#FBF9F8;
            width:80%;
            border: 0.6px solid #C8C5C4;
        }
    }
    input{
        margin-top:1em;
    }
    .inputs{
        margin-right:8em;
        margin-left:3em;
    }
</style>