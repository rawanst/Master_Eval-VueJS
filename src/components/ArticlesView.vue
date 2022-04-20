<template>
    <b style="margin-left:50em;" v-if="loggedIn === true">Bonjour {{this.pseudo}}</b>
    <!-- Ajouter un nouveau article si l'utilisateur est connecté -->
    <div class="row quoiDeNeuf" v-if="loggedIn === true">
        <h4>Quoi de neuf ? <i class="fa-solid fa-house"></i></h4>
        <form>
            <textarea class="form-control" placeholder="laisser un nouveau post" name="contenu" v-model="contenu"></textarea>
            <input class="form-control" type="text" placeholder="url de votre image - taille conseillée 1000x200px" name="urlImgArticle"  v-model="urlImgArticle"/>

            <input type="submit" class="btn btn-success" value="Laisser un nouveau post" style="width:100%;margin-bottom:1em;" @click="nouveauArticle" />
        </form>
    </div>
    <!-- S'il n'ya pas d'articles -->
    <div v-if="this.$store.articles == undefined ">
        <h2>Il n'existe pas d'articles pour le moment.</h2>
        <h2 v-if="!loggedIn">Connectez/Inscrivez-vous et créer vos articles.</h2>
        <h2 v-if="loggedIn">Créer vos articles.</h2>
    </div>
    <!-- Les articles -->
    <div v-for="(article, index) in this.$store.articles " :key="index" :class="post">
        <div class="card" style="min-width: 75rem; margin-bottom: 2rem">
            <div class="card-header d-flex justify-content-between">
                    <div class="user">
                        <img :src="article.urlImgProfil" class="photoUser-img"/>
                        <b style="font-size: 1.2em;">{{ article.pseudo }}</b> 
                    </div>
                    <div class="datePost">posté le {{article.date}}</div>
            </div>
            <div class="card-body" style="padding:0 ; display: flex; flex-direction: column;">
                <img :src="article.urlImgArticle"  style="padding: 0"/>
                {{article.contenu}}
            </div>
            <div class="card-footer">
                <div class="numberInterac d-flex justify-content-around" style="width: 160px">
                    <div class="d-flex justify-content-around" style="width: 60px">
                        <svg xmlns="http://www.w3.org/2000/svg" width="26" height="26" fill="currentColor" class="bi bi-chat-dots-fill mb-1" viewBox="0 0 16 16">
                            <path d="M16 8c0 3.866-3.582 7-8 7a9.06 9.06 0 0 1-2.347-.306c-.584.296-1.925.864-4.181 1.234-.2.032-.352-.176-.273-.362.354-.836.674-1.95.77-2.966C.744 11.37 0 9.76 0 8c0-3.866 3.582-7 8-7s8 3.134 8 7zM5 8a1 1 0 1 0-2 0 1 1 0 0 0 2 0zm4 0a1 1 0 1 0-2 0 1 1 0 0 0 2 0zm3 1a1 1 0 1 0 0-2 1 1 0 0 0 0 2z"/>
                        </svg>
                        <button type="button" class="btn btn-success btn-sm mb-1" disabled>{{article.commentaires.length}}</button>
                        
                    </div>
                    <div class="d-flex justify-content-around" style="width: 60px">
                        <svg xmlns="http://www.w3.org/2000/svg" width="26" height="26" fill="currentColor" class="bi bi-chat-heart-fill mb-1" viewBox="0 0 16 16">
                            <path d="M8 15c4.418 0 8-3.134 8-7s-3.582-7-8-7-8 3.134-8 7c0 1.76.743 3.37 1.97 4.6-.097 1.016-.417 2.13-.771 2.966-.079.186.074.394.273.362 2.256-.37 3.597-.938 4.18-1.234A9.06 9.06 0 0 0 8 15Zm0-9.007c1.664-1.711 5.825 1.283 0 5.132-5.825-3.85-1.664-6.843 0-5.132Z"/>
                        </svg>
                        <button type="button" class="btn btn-primary btn-sm mb-1" disabled>{{article.like}}</button>
                         
                    </div>
                </div>
                <div class="comments" v-for="(commentaire, index) in article.commentaires " :key="index">
                    <div class="card" style="min-width: 60rem; margin-bottom: 0.25rem">
                        <div class="card-header d-flex justify-content-between">
                            <div class="user">
                                <b>{{ commentaire.pseudo }}</b>
                                <p>{{commentaire.contenu}}</p>
                            </div>
                            <div class="content">
                                <p>le {{commentaire.dt }}</p>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="row addCommentaire" v-if="loggedIn === true">
                    <hr style="margin-left:0.5em;margin-top:1em;">
                    <h4>Ajouter un commentaire <i class="fa-regular fa-comment"> :</i></h4>
                    <form>
                        <textarea class="form-control" placeholder="laisser un commentaire" name="contenu" v-model="contenuCommentaire"></textarea>

                        <input type="submit" class="btn btn-outline-success" value="Laisser un commentaire" style="width:100%;margin-bottom:1em;" @click="(e)=>{addCommentaire(e,article)}"/>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data (){
        return {
            contenu : "",
            urlImgArticle : "",
            contenuCommentaire : "",
            articles : [],
            users : [],
            loggedIn : false,
            pseudo : ''
        }
    },
    created(){
        if (localStorage.getItem("loggedIn") === "true")  {
            this.loggedIn = true
            this.pseudo = localStorage.getItem("pseudoUser")
        }else{
            this.loggedIn = false
            this.pseudo = ''
        } 
    },
    methods:{
        nouveauArticle(e){
            e.preventDefault()
           
            const aujourdui = new Date();
            const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric', hour:'numeric', minute: 'numeric', second :'numeric' };
            const dateArticle = aujourdui.toLocaleDateString('fr-FR', options)
            const article = {
                contenu : this.contenu,
                like : 0,
                pseudo : this.$store.pseudo,
                date : dateArticle ,
                commentaires : [],
                urlImgArticle : this.urlImgArticle,   
            }
            fetch("http://localhost:3004/articles", {
                method: "post", 
                headers : {"content-type": "application/json"} , 
                body : JSON.stringify(article)
            })
            .then(reponse => reponse.json())
            .then(data => {
                this.getArticles()
                this.contenu=""
                this.urlImgArticle=""
                
            })
            
        },
        like(e,article){
            e.preventDefault()
            article.like++;
            fetch("http://localhost:3004/articles/"+article.id, {
                method: "put", 
                headers : {"content-type": "application/json"} , 
                body : JSON.stringify(article)
            })
            .then(reponse => reponse.json())
            .then(data => {
                this.getArticles()              
            })
        },
        addCommentaire(e,article){
            e.preventDefault()
            const aujourdui = new Date();
            const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric', hour:'numeric', minute: 'numeric', second :'numeric' };
            const dateCMT = aujourdui.toLocaleDateString('fr-FR', options)
            const commentaire = {
                contenu : this.contenuCommentaire,
                pseudo : this.$store.pseudo,
                dt: dateCMT
            }
            article.commentaires.push(commentaire)
            fetch("http://localhost:3004/articles/"+article.id, {
                method: "put", 
                headers : {"content-type": "application/json"} , 
                body : JSON.stringify(article)
            })
            .then(reponse => reponse.json())
            .then(data => {
                this.getArticles()
                this.contenuCommentaire=""                
            })
        },
        getArticles(){
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
                this.$forceUpdate();
            })
        }
    },
   beforeCreate: function(){
    
  },
  mounted:function(){
    fetch("http://localhost:3004/utilisateurs")
        .then(reponse => reponse.json())
        .then(data => {
            this.users = data ; 
    })
    fetch("http://localhost:3004/articles")
        .then(reponse => reponse.json())
        .then(data => {
            this.articles = data ; 
            this.articles.forEach(article => {
                this.users.forEach(user => {
                    if(article.pseudo == user.pseudo) article.urlImgProfil = user.urlImgProfil
                });
                this.users= []
            });
            this.$forceUpdate();
    })
    if (localStorage.getItem("loggedIn") === "true")  {
        this.loggedIn = true
        this.pseudo = localStorage.getItem("pseudoUser")
    }else{
        this.loggedIn = false
        this.pseudo = ''
    } 
  }
}
</script>

<style>
input{
    margin-top:0.5em;
  }
</style>