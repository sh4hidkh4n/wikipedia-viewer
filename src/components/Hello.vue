<template>
  <div id="hello">
    <div class="row flex">
      <div class="col s12 m12">
        <h1>Search Articles</h1>
      </div>

      <div class="col s12 m12">
        <form class="query-form" v-on:submit.prevent>
          <div class="input-field">
            <input v-model="query" id="query-box" @keyup.enter="fetchArticles" type="text" class="validate" autocomplete="off">
            <label for="query-box">Query</label>
        </div>
        </form>
      </div>
    </div>

    <div v-if="showArticleBox" class="article-container">
     <div class="col s12 m12">

     <ul class="collapsible popout" data-collapsible="accordion">
      <li v-for="(item, index) in items" >
        <div :class="(index==0) ? 'collapsible-header active':'collapsible-header'">
          <i class="material-icons">list</i>
          {{item.title}}
        </div>
        <div class="collapsible-body">
          <span>{{item.extract}}</span>
          <div class="col">
            <a :href="wikiPostUrl + item.pageid" class="link" target="_blank">
              <i class="material-icons">open_in_new</i>
            </a>
          </div>
        </div>
      </li>
      </ul>
      </div>
    </div>

  </div>
</template>

<script>

export default {
  name: 'hello',
  watch:{
    items:()=>{
      setTimeout(function() {
           $(document).ready(function(){
              $('.collapsible').collapsible();
              console.log("Ok")
            });
        }, 500);
    }
  },
  data () {
    return {
      wikiPostUrl: 'https://en.wikipedia.org/?curid=',
      showArticleBox:  false,
      query: '',
      api:'https://en.wikipedia.org/w/api.php?format=json&action=query&generator=search&gsrnamespace=0&gsrlimit=10&prop=pageimages|extracts&pilimit=max&exintro&explaintext&exsentences=1&exlimit=max&gsrsearch=',
      items: []
    }
  },
  
  methods: {
    fetchArticles(){
      console.log("Fetching..", this.$http);
      $("#query-box").addClass("loading");
      $.ajax({
          url: this.api+ this.query,
          success: response => {
                $(".flex").removeClass("flex").addClass("translateToTop");
                $("#query-box").removeClass("loading");
                this.showArticleBox = true;
                console.log(response.query)
                if(response.query == undefined){
                  Materialize.toast("No results matched to given query", 2000)
                  $(".query-box").focus()
                }else{
                  var results = response.query.pages;
                  var keys = Object.keys(results)
                  console.log(results)
                  this.items = []
                  keys.forEach( k=>{
                    this.items.push(results[k])
                  });
                }
          },
          dataType: 'jsonp'

          });
      }
      
    }
  }
</script>

<style>

.input-field label{ 
  color: #2c3e50 !important;
  transform: translateX(45%);
}

.input-field label:not(.label-icon).active{
  transform: translateY(-200%);
  font-size: 1em;
  margin-left: 0px;
}

#query-box{
  border: 1px solid #2c3e50;
  border-radius: 0.5em;
  font-size: 1em;
  text-align: center;
}

.flex{
  margin-top: 20%;
}

.translateToTop{
  transform: translateY(-10%);
  transition: all 1s ease-in;
}
.link{
  cursor: pointer;
}

.loading{
  background-color: #ffffff;
  background-image: url("https://i.imgur.com/5iJZPLO.gif");
  background-size: 25px 25px;
  background-position:99% center;
  background-repeat: no-repeat;
}
.article-container{
  text-align: left;
}
.collapsible-body{
  background-color: #2c3e50;
  color: #fff;
  border-bottom: none;
}
.collapsible-header{
  color: #fff;
  background-color: #1D263B;
  border-bottom: 1px solid #1D263B;
}
</style>
