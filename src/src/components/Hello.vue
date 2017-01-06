<template>
  <div class="hello">


    <input v-model="search">
    <h1>{{ msg }}</h1>
    <ul>
      <li v-for="sponsor in filteredSponsors">
        <h2><a v-bind:href="sponsor.url">{{ sponsor.name }}</a></h2>
      </li>
    </ul>

</template>

<script>
import _ from 'lodash'
export default {
  name: 'hello',
  created: function () {
    this.fetchData()
  },
  methods: {
    fetchData: function () {
      var self = this
      var request = new XMLHttpRequest()
      request.open('GET', 'static/data.json', true)
      request.onload = function () {
        if (request.status >= 200 && request.status < 400) {
          self.sponsors = JSON.parse(request.responseText).sponsors
        } else {
          console.log('Can\'t fetch the sponsor data.')
        }
      }
      request.send()
    }
  },
  data () {
    return {
      sponsors: {},
      msg: 'Welcome to Your Vue.js App',
      search: ''
    }
  },
  computed: {
    filteredSponsors: function () {
      if (this.search === '') {
        return this.sponsors
      }
      var self = this
      return _.filter(this.sponsors, function (sponsor) {
        return sponsor.name.toLowerCase().indexOf(self.search.toLowerCase()) !== -1
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  /*display: inline-block;*/
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
