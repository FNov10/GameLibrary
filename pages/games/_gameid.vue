<template>
    <Loading v-if = "$fetchState.pending"/>
    <div v-else class="container single-game">
        <NuxtLink class = "button" :to= "{name: 'index'}"> Back </NuxtLink>

        <div class="game-info">
            <div class = "game-img">
                <img
                    :src = "`//images.igdb.com/igdb/image/upload/t_cover_big/${this.url.substr(44)}`"
                    alt=""
                    href="google.com"
                    /> 

            </div>

            <div class ="game-content">
                <h1> Title: {{this.game.name}}</h1>
                <a  class = "button" :href="purchase"  > Purchase   </a>
                <p class = "game-fact tagline">
                    <span>Summary: </span>"{{game.summary}}"
                </p>
                <p class = "game-fact">
                    <span>Released: </span>
                    {{
                         date = new Date(this.game.first_release_date*1000).toLocaleString('en-us',{
                        month: 'long',
                        day: 'numeric',
                        year: 'numeric',
                        })
                    }}
                </p>
                <p class = "game-fact">
                    <spam>Storyline: </spam>{{game.storyline}}
                    
                </p>

                <p class = "game-fact">
                    <spam>Total rating(Average external critic rating): </spam>{{Math.round(game.total_rating)}}
                    
                </p>

                
                

            </div>
        </div>

        
    </div>
</template>

<script>
import axios from 'axios'
import Loading from '../../components/loading.vue'
export default {
    name: "single-game",
    head() {
        return {
            title: this.game.name,
        }
    },
    data() {
        return {
            game: '',
            cover:null,
            urls:[],
            steam:null,
            epic:null,
            official: null,
            wiki: null,
            purchase: null
            
        }
    },
    async fetch() {
        await this.getSingleGame()
    },
    fetchDelay: 1000,
    methods: {
        async getSingleGame() {
            console.log(this.$route.params.gameid)
            const data7 = axios({
                method: "post",
                url: "https://api.igdb.com/v4/games",
                headers: {
                    
                    "Client-ID": "uypnsdyuk91ln7vdb0uwzh0c8z87s6",
                    "Authorization": "Bearer 74uuc17os8xojw5ypdwj5jpvryfug5",
                },
                data: `fields *;  where id= ${this.$route.params.gameid}; `
            })
            const result = await data7
            
            this.game = result.data[0]
            this.cover= this.game.cover
            
            console.log(this.game.id)


            
            const data = axios({
                method: "post",
                url: "https://api.igdb.com/v4/covers",
                headers: {
                    
                    "Client-ID": "uypnsdyuk91ln7vdb0uwzh0c8z87s6",
                    "Authorization": "Bearer 74uuc17os8xojw5ypdwj5jpvryfug5",
                },
                data: `fields *;  where id= ${this.cover}; `
            })
            const result2 = await data
            
            this.url = result2.data[0].url
            
            console.log(this.url)


            const data3= axios({
                method: "post",
                url: "https://api.igdb.com/v4/websites",
                headers: {
                    
                    "Client-ID": "uypnsdyuk91ln7vdb0uwzh0c8z87s6",
                    "Authorization": "Bearer 74uuc17os8xojw5ypdwj5jpvryfug5",
                },
                data: `fields *;  where game= ${this.game.id} & category = 13;  `
            })
            const result3 = await data3
            
            result3.data.forEach(element => {
            if(element.category ===13){
             this.steam = element.url
            }
            if(element.category ===1){
             this.official = element.url
            }
            if(element.category ===3){
             this.wiki = element.url
            }
            if(element.category ===16){
             this.epic = element.url
            }
          
          
          
          
        })
            
            
            console.log(this.steam)
            console.log(this.epic)
            console.log(this.wiki)
            console.log(this.official)
            console.log(this.game.storyline==null)
            if(this.game.storyline == null){
                this.game.storyline = "Not available!"
            }

            if(this.steam != null){
                this.purchase = this.steam
            }

            else if(this.epic != null){
                this.purchase = this.epic
            }

            else if(this.official != null){
                this.purchase = this.official
            }

            else{
                this.purchase = this.game.url
            }




            
        },

        

    },
    components: { Loading }

    
}
</script>

<style lang="scss" scoped>
.single-game {
  color: #fff;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 32px 16px;
  .button {
    align-self: flex-start;
    margin-bottom: 32px;
  }
  .game-info {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 32px;
    color: #fff;
    @media (min-width: 800px) {
      flex-direction: row;
      align-items: flex-start;
    }
    .game-img {
      img {
        max-height: 500px;
        width: 100%;
        @media (min-width: 800px) {
          max-height: 700px;
          width: initial;
        }
      }
    }
    .game-content {
      h1 {
        font-size: 56px;
        font-weight: 400;
      }
      .game-fact {
        margin-top: 12px;
        font-size: 20px;
        line-height: 1.5;
        span {
          font-weight: 600;
          text-decoration: underline;
        }
      }
      .tagline {
        font-style: italic;
        span {
          font-style: normal;
        }
      }
    }
  }
}
</style>
