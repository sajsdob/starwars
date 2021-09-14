<template>
  <div class="container text-dark mt-5">

    <div class="row">
      <h1 class="text-center w-100 mb-5 text-danger font-weight-bold">Explore Star Wars Characters!</h1>
      <table id="table" class="table table-striped table-bordered" cellspacing="0" width="100%">
        <thead>
        <tr>
          <th class="pointer" v-on:click="sortName">Name
          <a class="pl-3">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-arrow-down-up" viewBox="0 0 16 16">
              <path fill-rule="evenodd" d="M11.5 15a.5.5 0 0 0 .5-.5V2.707l3.146 3.147a.5.5 0 0 0 .708-.708l-4-4a.5.5 0 0 0-.708 0l-4 4a.5.5 0 1 0 .708.708L11 2.707V14.5a.5.5 0 0 0 .5.5zm-7-14a.5.5 0 0 1 .5.5v11.793l3.146-3.147a.5.5 0 0 1 .708.708l-4 4a.5.5 0 0 1-.708 0l-4-4a.5.5 0 0 1 .708-.708L4 13.293V1.5a.5.5 0 0 1 .5-.5z"/>
            </svg>
          </a>
          </th>
          <th class="pointer"  v-on:click="sortHair">Hair Color
            <a class="pl-3" >
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-arrow-down-up" viewBox="0 0 16 16">
                <path fill-rule="evenodd" d="M11.5 15a.5.5 0 0 0 .5-.5V2.707l3.146 3.147a.5.5 0 0 0 .708-.708l-4-4a.5.5 0 0 0-.708 0l-4 4a.5.5 0 1 0 .708.708L11 2.707V14.5a.5.5 0 0 0 .5.5zm-7-14a.5.5 0 0 1 .5.5v11.793l3.146-3.147a.5.5 0 0 1 .708.708l-4 4a.5.5 0 0 1-.708 0l-4-4a.5.5 0 0 1 .708-.708L4 13.293V1.5a.5.5 0 0 1 .5-.5z"/>
              </svg>
          </a></th>
          <th>Starship</th>
          <th>Homeworld</th>
          <th>Films</th>
          <th>Edited</th>
        </tr>
        </thead>
        <tbody>
         <tr v-for="(person, index) in people" :key="index">
           <td class="col-2">{{person.name}}</td>
           <td class="col-2">{{person.hair_color}}</td>
           <td class="col-2">
             <ul>
               <li class="pointer" @click="passModalDetails(starship)"  v-for="(starship, index) in person.starships" :key="index">
                 <a class="text-primary">{{starship}}</a>
               </li>
             </ul>
           </td>
           <td class="col-2">{{person.homeworld}}</td>
           <td class="col-2">
               <ul>
                  <li class="pointer" @click="passModalDetails(film)" v-for="(film, index) in person.films" :key="index">
                    <a class="text-primary">{{film}}</a>
                  </li>
               </ul>
           </td>
           <td class="text-danger col-2 text-center" >{{new Date(person.edited).toISOString().slice(0, 10)}}</td>
         </tr>
        </tbody>
      </table>
      <div class="w-100 d-flex justify-content-center">
        <a role='button' type="button" class="btn btn-dark text-light mx-auto mb-5" @click="loadMorePeople()">Load More</a>
      </div>

      <div v-if="showModal" >
        <transition name="modal ">

          <div class="modal-mask">
            <div class="modal-wrapper">
              <div class="modal-dialog" role="document">
                <div class="modal-content ml-0 w-100" :class="loading ? 'loading' : ''" >
                  <div class="modal-header">
                    <h5 class="modal-title">{{modalInfo.title ? modalInfo.title : modalInfo.name}}</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                      <span class="pointer" aria-hidden="true" @click="closeModal">&times;</span>
                    </button>
                  </div>
                  <div class="modal-body" v-if="!loading">
                    <p><span class="font-weight-bold">{{ modalInfo.director ? 'Director: ' : 'Manufacturer: ' }} </span>{{modalInfo.director ? modalInfo.director : modalInfo.manufacturer}}</p>
                    <p><span class="font-weight-bold">{{ modalInfo.release_date ? 'Release Date: ' : 'Max atmosphering speed: ' }}  </span>{{modalInfo.release_date ? modalInfo.release_date : modalInfo.max_atmosphering_speed}}</p>
                    <p><span class="font-weight-bold">{{ modalInfo.release_date ? 'Short Description: ' : 'Cost in Credits: ' }}</span>{{modalInfo.opening_crawl ? modalInfo.opening_crawl : modalInfo.cost_in_credits}}</p>
                  </div>

                  <div class="modal-footer">
                    <button v-if="!loading" type="button" class="btn btn-danger pointer font-weight-bold " @click="closeModal">Close</button>
                  </div>
                  <div class="spinner-box" v-if="loading">
                    <div class="three-quarter-spinner"></div>
                  </div>
                </div>
                </div>
              </div>
            </div>
        </transition>
      </div>
    </div>
  </div>

</template>
<script>
import axios from "axios";



export default {

  data: () => ({
    people: null,
    page: 1,
    currentSort: [
      {id: 'name', sort: false, type: 'asc'},
      {id: 'hair', sort: false, type: 'asc'}
    ],
    showModal: false,
    modalInfo: null,
    my: null






  }),

  computed: {

  },
    async mounted () {
      await this.fetchCharacters()

      await this.people.forEach(character => {
        axios.get(character.homeworld)
            .then(world => {
              return world.data.name
            })
            .then(a => {
              character.homeworld = a
            })

        //  character.films.forEach((url, index) => {
        //    let title =  axios.get(url)
        //   .then(title => {
        //     dupa = title.data.title
        //     return title
        //   })
        //    character.films[index] = title;
        // } )

      })

    },
  methods: {
     fetchCharacters: async function() {
      await axios.get('https://swapi.dev/api/people')
          .then(response => {
            this.people =  response.data.results;
            return response
          })
          .then(res => {
            console.log(res)
            this.resolveMulti()
          })
          .catch(error => console.log(error))
    },

    //SORTING START
    sortName(){
      if(this.currentSort[0].sort == false || this.currentSort[0].sort == true && this.currentSort[0].type == 'dsc') {
        this.people.sort((a, b) => a.name < b.name ? -1 : (a.name > b.name ? 1 : 0));
        this.currentSort[0].sort = true;
        this.currentSort[0].type = 'asc';
      }
      else {
        this.people.sort((a, b) => a.name > b.name ? -1 : (a.name < b.name ? 1 : 0));
        this.currentSort[0].type = 'dsc';
      }
    },
    sortHair(){
      if(this.currentSort[1].sort == false || this.currentSort[1].sort == true && this.currentSort[1].type == 'dsc') {
        this.people.sort((a, b) => a.hair_color < b.hair_color ? -1 : (a.hair_color > b.hair_color ? 1 : 0));
        this.currentSort[1].sort = true;
        this.currentSort[1].type = 'asc';
      }
      else {
        this.people.sort((a, b) => a.hair_color > b.hair_color ? -1 : (a.hair_color < b.hair_color ? 1 : 0));
        this.currentSort[1].type = 'dsc';
      }
    },
    //SORTING END

    //LOADING NEW CHARACTERS
    async loadMorePeople() {
      this.page ++
      await axios.get(`https://swapi.dev/api/people/?page=${this.page}`)
          .then(response => {
            response.data.results.forEach(a=>this.people.push(a))
          } )
          .catch(error => console.log(error))

      this.resolve()
    },
    //LOADING NEW END

    //RESOLVING URLs START
    resolve:   async function() {

      await this.people.forEach(character => {
        axios.get(character.homeworld)
            .then(world => {
              return world.data.name
            })
            .then(a => character.homeworld = a)
      })
    },
    //RESOLVING URLs END

    //OPENING and CLOSING FILMS MODAL START
     passModalDetails(url) {
      this.showModal = true;
      this.loading = true;
      this.modalInfo =   axios.get(url)
      .then(urlInfo => {
        this.modalInfo = urlInfo.data
        this.loading = false;
      })
    },
    closeModal(){
      this.showModal = false;
      this.modalInfo = null;
    }
    //OPENING and CLOSING FILMS MODAL END

  }
};
</script>
<style lang="scss" scoped>
.pointer {
  cursor: pointer;
}

table {
  max-width: none;
  table-layout: fixed;
  word-wrap: break-word;
}

.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .5);
  display: table;
  transition: opacity .3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;

}

.three-quarter-spinner {
  width: 50px;
  height: 50px;
  border: 3px solid #fb5b53;
  border-top: 3px solid transparent;
  border-radius: 50%;
  animation: spin .5s linear 0s infinite;
}
.spinner-box {
  width: 300px;
  height: 300px;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: transparent;
  position: fixed;
  top: calc(50% - 150px);
  left: calc(50% - 150px);
}

@keyframes spin {
  from {
    transform: rotate(0);
  }
  to{
    transform: rotate(359deg);
  }
}


.modal-content {
  min-height: 250px;

}


.loading {
 filter: blur(2px);
}


</style>