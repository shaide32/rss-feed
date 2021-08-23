<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <input v-model="user" placeholder="Enter github user id" />
    <table>
      <thead>
        <th>Name</th>
        <th>Description</th>
        <th>Url</th>
      </thead>
      <tbody>
        <tr v-for="repo in data" :key="repo.id">
          <td> {{repo.name}}</td>
          <td> {{repo.description}}</td>
          <td> {{repo.html_url}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>

import { ref } from "vue";
import { debounce } from "lodash";
import * as RSSParser from "rss-parser";

const parser = new RSSParser();

(async () => {
  let feed = await parser.parseURL('https://today.uic.edu/feed');
  console.log(feed.title);

  feed.items.forEach(item => {
    console.log(item.title + ':' + item.link)
  });

})();

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },

  setup() {
    const data = ref(null);
    const loading = ref(true);
    const error = ref(null);

    return {
      data,
      loading,
      error
    }
  },

  created() {
    this.debouncedFetchData = debounce(this.fetchData, 500)
  },

  data() {
    return {
      user: 'shai1436'
    }
  },

  watch: {
    user(newUser, oldUser){
      console.log(newUser, oldUser);
      if(newUser !== oldUser) {
        this.debouncedFetchData(newUser);
      }
    }
  },

  mounted() {
    this.fetchData(this.user);
  },

  methods: {
    async fetchData(user = "shai1436") {
      const url = `https://api.github.com/users/${user}/repos`;
      const response = await fetch(url);
      this.data = await response.json();
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

input {
    width: 30%;
    font-size: 20px;
    padding: 5px;
    border-radius: 5px;
}

table {
  margin: 20px auto;
  padding: 5px;
  width: 70%;
}

td, th {
  text-align: left;
  padding: 5px;
}

h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
