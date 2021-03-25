<template>
  <v-app id="main" >
    <v-main>
      <v-app-bar
          absolute
          color="#6A76AB"
          dark
          shrink-on-scroll
          prominent
          src="https://picsum.photos/1920/1080?random"
          fade-img-on-scroll
          scroll-target="#scrolling-techniques-3"
          id="header"
      >
        <template v-slot:img="{ props }">
          <v-img
              v-bind="props"
              gradient="to top right, rgba(100,115,201,.7), rgba(25,32,72,.7)"
          ></v-img>
        </template>

        <v-img
        src="./assets/logo.svg"
        max-height="150"
        max-width="50"
        id="logo"
        >
        </v-img>

        <v-text-field
            v-model="keyword"
            @input="onInput"
            filled
            label="search"
            clearable
            id="search-field"
        ></v-text-field>
        <v-btn icon>
          <v-icon>mdi-magnify</v-icon>
        </v-btn>
      </v-app-bar>
    </v-main>

    <v-main  id="content" class="indigo lighten-1 pt-2 pb-3 pl-16" >
        <v-row >
          <v-col v-for="i in gif" cols="3">
            <v-img :src="i.images.original.url" max-width="365px" max-height="150px"/>
            <v-btn
                fab elevation="2" class="mt-1 purple lighten-4 align-center" @click="share(i.images.original.url,i.title)"
            ><v-icon>mdi-share-variant</v-icon></v-btn>
          </v-col>
        </v-row>
    </v-main>
  </v-app>
</template>

<script>
import {GiphyFetch} from '@giphy/js-fetch-api'

export default {
  name: 'App',
  data: () => ({
    gif:{},
    load:false,
    keyword:'',
    timeout:null
  }),
  async created(){
    await this.loadRandomGif()
  },
  methods:{
    share(url,title){
      if(navigator.share){
        navigator.share({
          title:title,
          url:url
        })
        .then(()=>{
          console.log(`Thanks for share!`)
        })
        .catch(console.error)
      }
    },
    async onInput(){
      clearTimeout(this.timeout)
      this.timeout = setTimeout(()=>{
        this.search()
      },500)

    },
    async search(){
      try {
        const wrong = 'not found'
        if(this.keyword){
          fetch(`https://api.giphy.com/v1/gifs/search?api_key=IX6sdQlcEmpvuJ2sBLgoMdMZM6a2Sg4Q&q=${this.keyword}`)
              .then(response => response.json())
              .then(result =>{
                if(result.data.length == 0){
                  fetch(`https://api.giphy.com/v1/gifs/search?api_key=IX6sdQlcEmpvuJ2sBLgoMdMZM6a2Sg4Q&q=${wrong}&limit=1`)
                      .then(response => response.json())
                      .then(result =>{
                        this.gif = result.data
                      })
                }
                this.gif = result.data
              })
        }else {
          this.loadRandomGif()
        }

      }catch (e) {
        console.log(e)
      }

    },
    async loadRandomGif(){
      const gf = new GiphyFetch('IX6sdQlcEmpvuJ2sBLgoMdMZM6a2Sg4Q')
      const {data:gif} = await gf.trending({limit:40})
      this.gif = gif
    }
  },
}
</script>

<style>
#main{
  margin: 0 auto;
}
#header{
  display: flex;
  align-items: center;
  justify-content: space-around;
}
#search-field{
  width: 200px;
}
#logo{
  margin-right: 30%;
}
#content{
 margin-top: 128px;
}
</style>
