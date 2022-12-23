<template>
    <div>
      <md-drawer :md-active.sync="showNavigation" md-swipeable>
        <md-toolbar class="md-transparent" md-elevation="0">
          <span class="md-title">Currency Info</span>
        </md-toolbar>
  <md-list>
          <md-list-item>
            <router-link to="/">
              <span class="md-list-item-text">Exchange Rates</span>
            </router-link>
          </md-list-item>
  <md-list-item>
            <router-link to="/convert">
              <span class="md-list-item-text">Convert Currency</span>
            </router-link>
          </md-list-item>
  <md-list-item>
            <router-link to="/historical">
              <span class="md-list-item-text">Historical Rates</span>
            </router-link>
          </md-list-item>
  <md-list-item>
            <router-link to="/historicalratesinperiod">
              <span class="md-list-item-text">Historical Rates in a Period</span>
            </router-link>
          </md-list-item>
  <md-list-item>
            <router-link to="/historicalratesinperiodgraph">
              <span class="md-list-item-text">Historical Rates in a Period Graph</span>
            </router-link>
          </md-list-item>
        </md-list>
      </md-drawer>
      <md-toolbar class="md-primary">
        <md-button class="md-icon-button" @click="showNavigation = true">
          <md-icon>menu</md-icon>
        </md-button>
        <h3 class="md-title">Currency Info</h3>
      </md-toolbar>
    </div>
  </template>
  <script>
  export default {
    name: "nav-bar",
    data() {
      return {
        msg: "Welcome to Your Vue.js App",
        showNavigation: false
      };
    }
  };
  </script>
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  </style>