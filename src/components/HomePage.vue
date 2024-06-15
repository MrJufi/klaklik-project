<template>
  <div class="main">
    <div class="search-container">
      <input type="text" v-model="keyword" placeholder="Search Novel" class="search-input">
      <button @click="handleSearch" class="search-button">Search</button>
    </div>
    
    <div v-if="results.length > 0">
      <h2 style="text-align: left;">Search Results :</h2>

      <div v-for="result in results" :key="result.id" class="result">
        <img :src="result.thumbnail" alt="Thumbnail" class="thumbnail">
        <h3>{{ result.title }}</h3>
      </div>
      
      <button v-if="showMoreButton" @click="showMore" class="show-more-button">Show More</button>
    </div>

    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
  </div>

  <footer style="padding: 10px"> Copyright &#169; 2024 Jufianto. All Rights Reserved</footer>
</template>



<script>
import axios from 'axios'

export default {
  data() {
    return {
      results: [],
      keyword: '',
      offset: 0,
      limit: 10,
      showMoreButton: true,
      errorMessage: ''
    }
  },
  methods: {
    async handleSearch() {
      this.offset = 0
      this.results = []
      this.errorMessage = ''
      await this.fetchData()
    },
    async showMore() {
      this.offset += this.limit
      await this.fetchData()
    },
    async fetchData() {
      try {
        const formData = new FormData()
        formData.append('limit', this.limit)
        formData.append('offset', this.offset)
        formData.append('all', '')
        formData.append('keyword', this.keyword)

        const response = await axios.post('https://dvl.klaklik.com/gateway2/newsearch?version=3', formData, {
          headers: {
            'Content-Type': 'multipart/form-data',
            'APPTOKEN': 'klaklikapptoken',
            'DEV': 'isme'
          }
        })

        const data = response.data
        if (data.STATUS === 200) {
          const baseData = data.DATA[0].data

          this.results = this.results.concat(baseData)
          this.showMoreButton = baseData.length === this.limit
        } else {
          this.errorMessage = data.MESSAGE
          this.showMoreButton = false
        }
      } catch (error) {
        console.error('Error fetching data : ', error)
      }
    }
  },
  mounted() {
    this.handleSearch()
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600&display=swap');

  * {
    font-family: 'Poppins', sans-serif;
  }

  .main {
    text-align: center;
    margin-top: 40px;
    padding: 20px;
  }

  .result {
    display: flex;
    align-items: center;
    margin: 20px 0;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .title {
    font-size: 18px;
    font-weight: bold;
    margin: 0;
  }

  .thumbnail {
    width: 100px;
    height: auto;
    border-radius: 5px;
    margin-right: 20px;
  }

  .error {
    color: red;
    margin: 20px 0;
  }

  .search-container {
    display: flex;
    justify-content: center;
    margin-bottom: 20px;
  }

  .search-input {
    width: 300px;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px 0 0 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    outline: none;
  }

  .search-input:focus {
    border-color: #00ADB5;
  }

  .search-button {
    padding: 10px 20px;
    border: 1px solid #00ADB5;
    border-radius: 0 5px 5px 0;
    background-color: #00ADB5;
    color: #fff;
    cursor: pointer;
    outline: none;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .search-button:hover {
    background-color: #3be7f0;
  }

  .show-more-button {
    padding: 10px 20px;
    border: 1px solid #00ADB5;
    border-radius: 5px;
    background-color: #00ADB5;
    color: #fff;
    cursor: pointer;
    outline: none;
    margin-top: 20px;
  }

  .show-more-button:hover {
    background-color: #3be7f0;
  }
</style>


