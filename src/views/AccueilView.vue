<template>
  <main class="accueil">
    <div class="row container">
        <div class="all">
            <div class="posts">
                <ArticlesView/>
            </div>
        </div>
    </div>
  </main>
</template>
<script>
import ArticlesView from '../components/ArticlesView.vue'
export default {
    
    methods:{
        reloadPage(){
            this.$forceUpdate()
        }
    },
    components:{ArticlesView}
}
</script>

<style lang="scss">
    .photoUser { 
        height: 100%; 
    } 
    .photoUser-img{
        border-radius: 50%;
        height:40px;
        width:40px;
    }
    
</style>