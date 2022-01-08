<template>
  <div>
    <h1 id="title">Let's find your repository!</h1>
    <div id="searchDiv">
      <input id="searchInput" type="text" placeholder="Search" v-model="name"
        v-on:keyup.enter="
          getData();
          clearTable();
        "/>
      <button
        id="searchBtn"
        v-on:click="
          getData();
          clearTable();
        "
      >
        <b-icon-search />
      </button>
    </div>
    <h6 id="note">Write your username in the field above.</h6>
    <div v-if="filteredRepos && reposAmount != 0">
      <div id="table">
        <b-table
          striped
          hover
          :items="filteredRepos"
          :per-page="10"
          small
        ></b-table>
      </div>
      <b-pagination
        pills
        align="center"
        v-model="currentPage"
        :total-rows="reposAmount"
        :per-page="10"
        @change="pageChange"
        first-text="First"
        prev-text="Prev"
        next-text="Next"
        last-text="Last"
      >
      </b-pagination>
    </div>
    <div v-if="reposAmount === 0" class="danger">
      <p>User doesn't have any repositories</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Home",
  components: {},
  data() {
    return {
      filteredRepos: "",
      error: null,
      currentPage: 1,
      name: null,
      reposAmount: "",
    };
  },
  methods: {
    getData() {
      if (this.name !== null) {
        axios
          .get(
            `https://api.github.com/search/repositories?q=user:${this.name}&sort=stars&page=${this.currentPage}&per_page=10`
          )
          .then((response) => {
            const repos = response.data;

            this.filteredRepos = repos.items.map((o) => ({
              repository: o.name,
              stars: o.stargazers_count,
            }));

            this.reposAmount = repos.total_count;
          })
          .catch((e) => {
            this.error.push(e);
          });
      }
    },
    pageChange(value) {
      this.currentPage = value;
      this.getData();
    },
    clearTable() {
      this.filteredRepos = null;
      this.reposAmount = null;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  padding: 30px;
}

#searchDiv {
  position: relative;
  width: 300px;
  margin: 0px auto 10px;
}
#searchInput {
  width: 100%;
  border: 1px solid #ccc;
  border-radius: 100px;
  padding: 10px 100px 10px 20px;
  line-height: 1;
  box-sizing: border-box;
  outline: none;
}
#searchBtn {
  position: absolute;
  right: 3px;
  top: 3px;
  bottom: 3px;
  border: 0;
  background: #0d6efd;
  color: #fff;
  outline: none;
  margin: 0;
  padding: 0 10px;
  border-radius: 100px;
  z-index: 2;
}
#title {
  padding: 20px;
}
#note {
  padding: 0px 0px 20px;
}
#table {
  width: 50%;
  margin: 0px auto;
}
</style>
