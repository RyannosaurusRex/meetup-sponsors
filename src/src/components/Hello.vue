<template>
  <div class="hello">
    <label for="txtSearch">Search: </label><input id="txtSearch" v-model="search">
    <select v-model="searchSponsorshipType">
      <option value=""></option>
      <option v-for="type in sponsorTypes" v-bind:value="type">{{ type }}</option>
    </select>
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
      msg: 'Find a sponsor for your meetup!',
      search: '',
      searchSponsorshipType: ''
    }
  },
  computed: {
    filteredSponsors: function () {
      if (this.search === '' && this.searchSponsorshipType === '') {
        return this.sponsors
      }
      var self = this

      // Filter by search text.
      var results = _.filter(this.sponsors, function (sponsor) {
        if (self.search === '') {
          return true
        } else {
          return sponsor.name.toLowerCase().indexOf(self.search.toLowerCase()) !== -1
        }
      })

      // Filter by sponsorship type
      return _.filter(results, function (sponsor) {
        if (self.searchSponsorshipType === '') {
          return true
        } else {
          return sponsor.sponsorTypes.indexOf(self.searchSponsorshipType) !== -1
        }
      })
    },
    sponsorTypes: function () {
      // Return the unique sponsor types that sponsors are tagged with.
      return _.uniqBy([].concat.apply([], _.map(this.sponsors, 'sponsorTypes')))
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
