<template>
    <div class="page-container md-layout-column">
      <drawer></drawer>
      <md-content>
        <h1 class="center">Quotes as of {{currentTime}}</h1>
        <div class="container">
          <div class="md-layout-item">
            <md-field>
              <label for="movie">Base Currency</label>
              <md-select v-model="baseCurrency" name="baseCurrency">
                <md-option value="EUR">Euro</md-option>
                <md-option value="USD">US Dollar</md-option>
                <md-option value="CAD">Canadian Dollar</md-option>
              </md-select>
            </md-field>
          </div>
          <div>
            <md-content class="md-layout-item" v-for="(q, index) in quoteData.rates" :key="index">
              <h2>{{quoteData.base}} - {{q.symbol| currencyName}}</h2>
              <p>Price: {{q.rate}}</p>
            </md-content>
          </div>
        </div>
      </md-content>
    </div>
  </template>
  <script>
  import currencies from "../currencies";
  import { APIURL } from "../urls";
  import * as moment from "moment";
  const request = require("superagent");
  export default {
    name: "quotes",
    data() {
      return {
        quoteData: {},
        currentTime: moment().format("YYYY-MM-DD HH:mm:ss a"),
        baseCurrency: null,
        showNavigation: false
      };
    },
    methods: {
      getQuotes() {
        let url = `${APIURL}/latest`;
        if (this.baseCurrency) {
          url = `${APIURL}/latest?base=${this.baseCurrency}`;
        }
        request.get(url).end((err, res) => {
          this.quoteData = res.body;
          this.quoteData.rates = Object.keys(this.quoteData.rates).map(k => {
            return {
              symbol: k,
              rate: this.quoteData.rates[k]
            };
          });
          this.currentTime = moment().format("YYYY-MM-DD HH:mm:ss a");
        });
      }
    },
    beforeMount() {
      this.getQuotes();
    },
    watch: {
      baseCurrency: function() {
        this.getQuotes();
      }
    }
  };
  </script>
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style>
  </style>