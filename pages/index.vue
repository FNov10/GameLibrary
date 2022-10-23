
<template>
  <div class = "home">
    <!-- Hero -->
    <Hero/>

    <!--Search-->
    <div class="container search">
      <input @keyup.enter="$fetch"  
      type = "text" 
      placeholder="Search" 
      v-model.lazy="searchInput"
      />
      
      <button @click="clearSearch" v-show="searchInput!= ''" class = "button">Clear Search</button>
    </div>


    <!--Loading-->
    <Loading v-if="$fetchState.pending"/>


    <!--Games-->
    <div v-else class ="container games">

      <div v-if="searchInput !==''" id = "games-grid" class = "games-grid">
        <div class="game" v-for="(game,index) in urls2" :key = "index">
          <div class="game-img">

            
            <img 
              :src = "`//images.igdb.com/igdb/image/upload/t_cover_big/${game.substr(44)}`"
              alt =""
              />

              <p class="review">
              {{ "Rating: "+Math.round(games2[index].rating) }}
               
            </p>
              


              <p class="overview">
                {{games2[index].summary.slice(0,400)}}
                <span v-if = "games2[index].summary.length> 400"></span>
              </p>
          </div>
          <div class="info">
            
            <p class="title">
              {{ games2[index].name.slice(0,25) }}
               <span v-if = "games2[index].name.length> 25">...</span>
            </p>

            <p class = "release">
              Released:
              {{
                 date = new Date(games2[index].first_release_date*1000).toLocaleString('en-us',{
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric',
                 })
              }}
            </p>
            <NuxtLink class="button button-light" :to="{name: 'games-gameid', params: {gameid: games2[index].id}}"
            >Get more Info</NuxtLink>
          </div>
        </div>
      </div>


      <div v-else id = "games-grid" class = "games-grid">
        <div class="game" v-for="(game,index) in urls" :key = "index">
          <div class="game-img">

            
            <img 
              :src = "`//images.igdb.com/igdb/image/upload/t_cover_big/${game.url.substr(44)}`"
              alt =""
              />

              <p class="review">
              {{ "Rating: "+Math.round(games[index].rating) }}
               
            </p>
              


              <p class="overview">
                {{games[index].summary.slice(0,400)}}
                <span v-if = "games[index].summary.length> 400"></span>
              </p>
          </div>
          <div class="info">
            
            <p class="title">
              {{ games[index].name.slice(0,25) }}
               <span v-if = "games[index].name.length> 25">...</span>
            </p>

            <p class = "release">
              Released:
              {{
                 date = new Date(games[index].first_release_date*1000).toLocaleString('en-us',{
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric',
                 })
              }}
            </p>
            <NuxtLink class="button button-light" :to="{name: 'games-gameid', params: {gameid: games[index].id}}"
            >Get more Info</NuxtLink>
          </div>
        </div>
      </div>
      


      

    
    
    



      


      
      

    </div>
    
  </div>
  </template>
  
  <script>
  import axios from 'axios'
import Hero from '../components/Hero.vue';
import Loading from '../components/loading.vue';
  
  export default {
    name: "IndexPage",
    components: { Hero, Loading },
    head() {
        return {
            title: "Games Library",
        }
    },
    data() {
      return {
        games: [],
        images: [],
        urls:[],
        released: [],
        searchInput: '',
        searchedGames: [],
        games2:[],
        images2:[],
        urls2:[],
      }
    }, 

    async test(){

    },

   
    async fetch(){
      
      if(this.searchInput ===''){
        
        await this.getGanes()
        
        
      }
      else{
        
        
        await this.searchGames()
        

        
      }
      

    },

    fetchDelay: 1000,
    
    
    methods:{
      async getGanes() {
        const data = axios({
          method:'post',
          url: 'https://api.igdb.com/v4/games',
          headers:{
            'Accept': 'application/json',
            'Client-ID':'uypnsdyuk91ln7vdb0uwzh0c8z87s6',
            'Authorization': 'Bearer 74uuc17os8xojw5ypdwj5jpvryfug5',

          },
          
          data:`fields *; where cover>0; where total_rating<${Math.floor(Math.random() * 101)}; limit 20; sort id; `
          
        })
        const result = await data
        result.data.forEach(element => {
          this.games.push(element)
          this.images.push(element.cover)
          
          
        })
        
        
        

        const x = "(" + this.images.toString() + ")"
        
        
        const data2 = axios({
          method:'post',
          url: 'https://api.igdb.com/v4/covers',
          headers:{
            'Accept': 'application/json',
            'Client-ID':'uypnsdyuk91ln7vdb0uwzh0c8z87s6',
            'Authorization': 'Bearer 74uuc17os8xojw5ypdwj5jpvryfug5',

          },
          
          data:`fields url; where id =${x}; limit 500; sort game; `
          
        })
        const result2 = await data2
        result2.data.forEach(element => {
          this.urls.push(element)
          
          
        })
        
        

        
        
      },
      async searchGames (){
        const data3 = axios({
          method:'post',
          url: 'https://api.igdb.com/v4/search',
          headers:{
            'Accept': 'application/json',
            'Client-ID':'uypnsdyuk91ln7vdb0uwzh0c8z87s6',
            'Authorization': 'Bearer 74uuc17os8xojw5ypdwj5jpvryfug5',

          },
          
          data:`fields game; search "${this.searchInput}"; where game>0; limit 500;  `
          
        })
        const result3 = await data3
        result3.data.forEach(element => {
          this.searchedGames.push(element.game)
          
          
        })

        const y = "(" + this.searchedGames.toString() + ")"
        console.log(y)


        const data4 = axios({
          method:'post',
          url: 'https://api.igdb.com/v4/games',
          headers:{
            'Accept': 'application/json',
            'Client-ID':'uypnsdyuk91ln7vdb0uwzh0c8z87s6',
            'Authorization': 'Bearer 74uuc17os8xojw5ypdwj5jpvryfug5',

          },
          
          data:`fields *; where id = ${y};  limit 500; sort id;  `
          
        })
        const result4 = await data4
        result4.data.forEach(element => {
          if(element.cover != null && element.id !=null && element.rating!=null && element.summary!=null && element.first_release_date != null){
            this.games2.push(element)
            this.images2.push(element.cover)
          }
          
          
          
          
        })

        const x = "(" + this.images2.toString() + ")"
        console.log(x)

        const data5 = axios({
          method:'post',
          url: 'https://api.igdb.com/v4/covers',
          headers:{
            'Accept': 'application/json',
            'Client-ID':'uypnsdyuk91ln7vdb0uwzh0c8z87s6',
            'Authorization': 'Bearer 74uuc17os8xojw5ypdwj5jpvryfug5',

          },
          
          data:`fields url; where id =${x}; limit 500; sort game; `
          
        })
        const result5 = await data5
        result5.data.forEach(element => {
          this.urls2.push(element.url)
          
          
        })
        console.log(this.games2)
        
        
        
        
        

        
        
      },

      clearSearch() {
        this.searchInput = ''
        this.games2 = []
        this.images2 = []
        this.urls2 = []
        this.searchedGames = []
        
        
      }

      

      

      

      
    },
}
  </script>

<style lang="scss">


.search {
    display: flex;
    padding: 32px 16px;
    input {
      max-width: 350px;
      width: 100%;
      padding: 12px 6px;
      font-size: 14px;
      border: 5px solid grey;
      &:focus {
        outline: none;
      }
    }
    .button {
      border-top-left-radius: 0;
      border-bottom-left-radius: 0;
    }
  }
  .games {
    padding: 32px 16px;
    .games-grid {
      display: grid;
      column-gap: 32px;
      row-gap: 64px;
      grid-template-columns: 1fr;
      @media (min-width: 500px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 750px) {
        grid-template-columns: repeat(3, 1fr);
      }
      @media (min-width: 1100px) {
        grid-template-columns: repeat(4, 1fr);
      }
      .game {
        position: relative;
        display: flex;
        flex-direction: column;
        .game-img {
          position: relative;
          overflow: hidden;

          &:hover {
            .overview {
              transform: translateY(0);
            }
          }
          img {
            display: block;
            width: 100%;
            height: 100%;
          }

          .review {
            position: absolute;
            top: 0;
            left: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            width: 100px;
            height: 40px;
            background-color: #3A243B;
            color: #fff;
            border-radius: 0 0 16px 0;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
              0 2px 4px -1px rgba(0, 0, 0, 0.06);
          }
          .overview {
            line-height: 1.5;
            position: absolute;
            bottom: 0;
            background-color: #3A243B;
            padding: 12px;
            color: #fff;
            transform: translateY(100%);
            transition: 0.3s ease-in-out all;
          }
          
          
        }
        .info {
          margin-top: auto;

          
          .title {
            margin-top: 8px;
            color: #ff0;
            font-size: 20px;
          }
          .release {
            margin-top: 8px;
            color: #c9c9c9;
          }
          .button {
            margin-top: 8px;
          }
        }
      }
    }
  }

</style>
Footer
© 2022 GitHub, Inc.
