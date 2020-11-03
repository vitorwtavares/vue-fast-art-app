<template>
  <div id="app">
    <div v-bind:class="image !== '' ? 'container justify-start' : 'container justify-center'">
      <div v-if="loading" class="loading-container">
        <p class="loading">loading...</p>
      </div>
      <form @submit.prevent="fetchData">
        <input class="search" placeholder="find art." type="text" v-model="searchQuery" />
        <div class="image-container">
          <p class="title" v-if="image !== ''">{{title}}</p>
          <img v-if="image !== ''" class="image" v-bind:src="image" @load="finishedLoad">
        </div>
      </form>
      <p v-if="error" class="no-results">No results, try again.</p>
      <div :style="backgroundImage"/>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data () {
    return{
      base_url: 'https://collectionapi.metmuseum.org/public/collection/v1/',
      searchQuery: '',
      image: '',
      title: '',
      error: false,
      loading: false
    }
  },
  methods: {
    async fetchData () {
      try {
        this.loading = true
        const searchResponse = await fetch(`${this.base_url}search?q=${this.searchQuery}`)
        const searchData = await searchResponse.json()
        const objectResponse = await fetch(`${this.base_url}objects/${searchData.objectIDs[0]}`)
        const objectData = await objectResponse.json()
        this.image = objectData.primaryImage
        this.title = objectData.title
        this.error = false
      } catch (err) {
        this.setError()
      } finally {
        this.searchQuery = ''
      }
    },
    setError () {
      this.error = true
      this.image = ''
      this.loading = false
    },
    finishedLoad () {
      this.loading = false;
    }
  },
  computed: {
    backgroundImage () {
      return {
        "background-image": `url("https://images.unsplash.com/photo-1598103586054-c4b456cbddc4?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9")`,
        "background-size": "50%",
        "background-position": "center",
        "background-repeat": "no-repeat",
        position: "absolute",
        width: "100vw",
        height: "100vh",
        "z-index": -1,
        filter: "grayscale(100%)"
      }
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Raleway", sans-serif;
}

.loading-container {
  position: absolute;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0,0,0,0.8);
}

@keyframes loading {
	0%,30%{ letter-spacing: 10px; }
	70%,100%{ letter-spacing: 30px; }
}

.loading {
  color: white;
  font-size: 32px;
  letter-spacing: 10px;
  animation: loading 2s ease-in-out infinite alternate;
}

.container {
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.justify-center {
  justify-content: center;
}

.justify-start {
  justify-content: flex-start;
}

form {
  display: flex;
  align-items: center;
  flex-direction: column;
  width: 100%;
  height: 100%;
}

.image-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
}

.image{
  max-width: 60%;
  max-height: 80vh;
  border: none;
  border-radius: 15px;
  transition: 0.4s;
}

.image:hover {
  box-shadow: 0 0 50px 20px rgba(0,0,0,0.1);
}

.title {
  font-size: 30px;
  margin: 0px 30px 30px;
  text-align: center;
}

.search {
  border: none;
  border-radius: 4px;
  background-color: #222;
  box-shadow: 0 0 15px  rgba(0,0,0,0.4);
  color: white;
  margin-top: 20px;
  padding: 10px 15px;
  width: 270px;
  outline: none;
  transition: 0.4s;
  font-size: 16px;
  text-align: center;
}

.search::placeholder { 
  color: white;
  font-size: 16px;
  text-align: center;    
  transition: 0.4s;
}

.search:hover {
  width: 320px;
  transition: 0.4s;
}

.search:hover::placeholder {
  letter-spacing: 10px;
  transition: 0.4s;
}

.search:focus {
  width: 320px;
  box-shadow: 0 0 25px  rgba(0,0,0,0.4);
}

.search:focus::placeholder {
  opacity: 0;
}

.no-results {
  margin-top: 50px;
  font-size: 30px;
  font-weight: 600;
  color: white;
}

</style>
