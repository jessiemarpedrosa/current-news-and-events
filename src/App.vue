<template>
  <div id="app">
    <div class="container">

    <div class="jumbotron pt-4 pb-4 mt-4 mb-4 bg-info text-white">
      <h1>Global News & Events v1.0</h1>
      <p>This project is done using VueJS, NewsAPI, RestcountriesAPI and Bootstrap 4.0.</p>
      <small>Author : Jessiemar Pedrosa</small>
    </div>

    <div class="container filters">
      <form class="row" @submit.prevent="loadQuote(selectedCountry,selectedCategory)">
        <div class="col-6 col-xs-12">  
            <div class="row">
              <label for="country" class="mr-2 font-weight-bold col-xs-4">Select Country:</label>
              <select name="country" id="country" filterable class="form-control col-xs-12 col-md-8" tabindex="12" 
                v-model="selectedCountry">
                  <option v-for="(country, index) in countries" 
                          :key="index" 
                          :value="country.alpha2Code" >{{ country.name }}
                  </option>
              </select>         
            </div>
        </div>
        <div class="col-6 col-xs-12">
            <div class="row">
              <label for="category" class="mr-2 font-weight-bold col-xs-4">Select Category:</label>
              <select name="category" id="category" class="form-control col-xs-12 col-md-8" v-model="selectedCategory">
                  <option v-for="category in categories" 
                          :key="category" 
                          :value="category" >{{ category }}
                  </option>
              </select>
            </div>
        </div>
        
        <button type="submit" class="btn btn-info btn-lg btn-block mt-4 text-uppercase">Submit</button>
      </form>
    </div>

    <div class="spacer"></div>

    <div class="status"><strong>{{ status }}</strong></div>

    <div class="row article-container">
        <div class="article col-sm-6 col-md-4 col-lg-3 mb-4" v-for="(data,index) in result" :key='index'>
          <div class="card" style="">
            <img :src="data.urlToImage" class="card-img-top" :alt="data.title">
            <div class="card-body">
              <h5 class="card-title"><a target="_blank" :href="data.url">{{ data.title }}</a></h5>
              <p class="card-text">{{ data.body }}</p>
              <span class="card-source">Source : {{ data.source.name }}</span>
              <span class="card-author">Author : {{ data.author }}</span>
              <span class="card-published-date">Published : {{ data.publishedAt | formatDate }}</span>
              <a target="_blank" :href="data.url" class="btn btn-success btn-sm btn-block">Read More</a>
            </div>
          </div>
        </div>
    </div>

    <div class="spacer"></div>

    <!-- Content here -->
    </div>
  </div>
</template>

<script>
const axios = require("axios");
//import { Carousel, Slide } from 'vue-carousel';

export default {
  name: 'app',
  data() {
    return{
      status: 'Please select country & category on the dropdown.',
      result: [],
      author: '',
      title: '',
      body: '',
      urlToImage: '',
      url: '',
      selectedCountry: null,
      selectedCategory: null,
      countries: [],
      getCountry: '',
      categories: ['General','Business','Technology','Sports','Health','Science','Entertainment','Travel'],
      source: []
    }
  },
  created: function(){
    this.loadQuote();
  },
  methods: {
    loadQuote: function(selectedCountry,selectedCategory){
      this.status = 'Loading Articles...';
      var vm = this;
      // console.log("Country selected is : " + selectedCountry)
      console.log(selectedCategory + ' - ' + selectedCountry);

    axios({
      "method":"GET",
      "url":"https://cors-anywhere.herokuapp.com/https://newsapi.org/v2/top-headlines?country=" 
            + selectedCountry + "&category=" + selectedCategory + "&apiKey=d1ab724144044932a70e999c6872f406",
      "headers":{
        "content-type":"application/octet-stream",
        "Access-Control-Allow-Origin": "*"
      }
    })
    .then((response)=>{
      if( response.data.totalResults > 0 ){        
        var searchedCountryName = ''
        for(var i=0;i<vm.countries.length;i++){
          if(vm.countries[i].alpha2Code === selectedCountry){
            searchedCountryName = vm.countries[i].name
          }
        }
        vm.status = this.selectedCategory + ' News found in ' + searchedCountryName
        vm.result = response.data.articles;  
      } else {
        vm.status = 'No articles found.'
        vm.result = ''
      }
      // console.log("Total Results: " + response.data.totalResults)
      })
      .catch((error)=>{
        vm.status = error
        console.log(error)
      })

    axios.get("https://cors-anywhere.herokuapp.com/https://restcountries.eu/rest/v2/all", {'Content-Type': 'application/json'}, { headers: { 'Access-Control-Allow-Origin': '*' }} ).then( response => {
      vm.countries = response.data;
    })
      
    }
  },
  components: {
    //Carousel,
    //Slide
  }
}

</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0px;
  padding: 0;
}
a { color: black; }
img {
  max-width: 100%;
}
.article .card{
  padding: 1rem;
  text-align: left; 
}
.article .card-body{
  padding: 1rem 0;
}
.article .card span.card-source, .article .card span.card-author, .article .card span.card-published-date {
    font-size: 0.8rem;
    line-height: normal;
    margin-bottom: 5px;
    display: block;
    width: 100%;
}
.spacer{
  height: 40px; 
}
.status{
  margin-bottom: 20px;
}
h5.card-title{
  font-size: 1rem;
}
.filters .row{
  align-items: flex-end;  
}

@media only screen and (max-width: 480px) {
  h1 {
    font-size: 1.5em;
  }
}
</style>
