<template>
    <div class="page-container md-layout-column">
      <drawer></drawer>
      <div class="container">
        <h1 class="center">Historical Exchange Rates</h1>
        <form @submit.prevent="getHistoricalRate">
          <div class="md-layout-item">
            <label>Date</label>
            <md-datepicker
              v-model="form.date"
              v-validate="'required'"
              name="date"
              :md-disabled-dates="futureDates"
            ></md-datepicker>
            <md-field :class="{'md-invalid': errors.first('baseCurrency') }">
              <label for="baseCurrency">Currency Symbol</label>
              <md-select v-model="form.baseCurrency" name="baseCurrency" v-validate="'required'">
                <md-option :value="s.code" v-for="s in symbols" :key="s.code">{{s.name}}</md-option>
              </md-select>
              <span class="md-error">{{ errors.first('baseCurrency') }}</span>
            </md-field>
          </div>
          <md-button class="md-dense md-raised md-primary" type="submit">Find Rate</md-button>
        </form>
        <div>
          <md-content class="md-layout-item" v-for="(r, index) in rates" :key="index">
            <h2>{{form.baseCurrency | currencyName}} - {{r.symbol| currencyName}}</h2>
            <p>{{r.rate}}</p>
          </md-content>
        </div>
      </div>
    </div>
  </template>
  <script>
  import currencies from "../currencies";
  import { APIURL } from "../urls";
  import * as moment from "moment";
  const request = require("superagent");
  export default {
    data() {
      return {
        form: {
          date: new Date(),
          baseCurrency: ""
        },
        rates: [],
        symbols: [],
        currencies
      };
    },
    methods: {
      getHistoricalRate(evt) {
        this.hasResult = false;
        evt.preventDefault();
        if (this.errors.items.length > 0) {
          return;
        }
  request
          .get(
            `${APIURL}/${moment(this.form.date).format("YYYY-MM-DD")}?base=${
              this.form.baseCurrency
            }`
          )
          .end((err, res) => {
            let body = res.body;
            this.hasResult = true;
            this.rates = Object.keys(body.rates).map(k => {
              return {
                symbol: k,
                rate: body.rates[k]
              };
            });
          });
      },
      futureDates(evt) {
        return +evt > +new Date();
      }
    },
    beforeMount() {
      this.symbols = Object.keys(this.currencies).map(k => {
        return {
          symbol: k,
          ...this.currencies[k]
        };
      });
    }
  };
  </script>
  <style>
  </style>