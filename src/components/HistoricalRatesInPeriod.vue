<template>
    <div class="page-container md-layout-column">
      <drawer></drawer>
      <div class="container">
        <h1 class="center">Historical Exchange Rates in a Period</h1>
        <form @submit.prevent="getHistoricalRate">
          <div class="md-layout-item">
            <label>Start Date</label>
            <md-datepicker
              v-model="form.startDate"
              v-validate="'required'"
              name="date"
              :md-disabled-dates="invalidStartDates"
            ></md-datepicker>
            <label>End Date</label>
            <md-datepicker
              v-model="form.endDate"
              v-validate="'required'"
              name="date"
              :md-disabled-dates="invalidEndDates"
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
            <h2>{{r.date}}</h2>
            <md-content class="md-layout-item" v-for="(rr, index) in r.rates" :key="index">
              <h3>{{form.baseCurrency | currencyName}} - {{rr.symbol| currencyName}}</h3>
              <p>{{rr.rate}}</p>
            </md-content>
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
          startDate: new Date(),
          endDate: new Date(),
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
            `${APIURL}/history?start_at=${moment(this.form.startDate).format(
              "YYYY-MM-DD"
            )}&end_at=${moment(this.form.endDate).format("YYYY-MM-DD")}&base=${
              this.form.baseCurrency
            }`
          )
          .end((err, res) => {
            let body = res.body;
            this.hasResult = true;
            this.rates = Object.keys(body.rates)
              .map(date => {
                return {
                  date: moment(date).format("YYYY-MM-DD"),
                  rates: Object.keys(body.rates[date]).map(k => {
                    return {
                      symbol: k,
                      rate: body.rates[date][k]
                    };
                  })
                };
              })
              .sort((a, b) => +new Date(a.date) - +new Date(b.date));
          });
      },
      invalidStartDates(evt) {
        return +evt > +new Date();
      },
      invalidEndDates(evt) {
        return +evt < +this.form.startDate || +evt > +new Date();
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