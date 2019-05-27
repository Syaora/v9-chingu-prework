<template>
  <b-container>
    <b-row>
      <h1>Meteorite Explorer</h1>
    </b-row>

    <b-row class="mt-5">
      <b-col md="8" offset-md="2">
        <b-input-group>
          <b-form-input placeholder="text" v-model="name"></b-form-input>
          <b-input-group-append>
            <b-button variant="outline-secondary" @click="queryList()">Search</b-button>
          </b-input-group-append>
        </b-input-group>
      </b-col>
    </b-row>

    <b-row class="text-left mt-4">
      <b-table striped hover :items="currentList" :per-page="13" :current-page="currentPage" :fields="fields">
        <template slot="year" slot-scope="data">
          {{ data.unformatted.slice(0, 4) }}
        </template>
      </b-table>
    </b-row>

    <b-row>
       <b-pagination v-model="currentPage" align="center" :total-rows="rows" :per-page="13"></b-pagination>
    </b-row>
  </b-container>
</template>

<script>
export default {
  data () {
    return {
      list: [],
      fields: [
        'name', 
        'id', 
        'nametype', 
        'recclass', 
        { key: 'mass', label: 'Mass(g)'}, 
        'fall', 
        'year', 
        { key: 'geolocation.latitude', label: 'Latitude'}, 
        { key: 'geolocation.longitude', label: 'Longitude'}
        ],
      url: 'https://data.nasa.gov/resource/gh4g-9sfh.json?$limit=50000',
      name: "",
      currentPage: 1
    }
  },
  methods: {
    originalList: function() {
      fetch(this.url).then(res => res.json()).then(data => {
        this.list = data;
      })
    },
    queryList: function() {
      if (this.name === "") {
        this.originalList();
      } else {
        fetch(this.url + "&$where=lower(name) like '%25" + this.name.toLowerCase() + "%25'").then(res => res.json()).then(data => {
          this.list = data;
        })
      }
    }
  },
  computed: {
    currentList: function() {
      return this.list;
    },
    rows: function() {
      return this.list.length;
    }
  },
  mounted () {
    this.originalList();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
  width: 100%;
}

.b-pagination {
  width: 100%;
}
</style>
