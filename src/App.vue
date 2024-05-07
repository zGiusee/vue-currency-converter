<script>
import { store } from '../store.js';
import Input_select from './components/Input_select.vue';
import axios from 'axios';
export default {
  components: {
    Input_select,
  },
  data() {
    return {
      store,
      // currenciesObj: [],
      currencies: [],
    }
  },
  mounted() {
    this.getCurrencies();
  },
  created() {

  },
  methods: {

    getCurrencies() {
      axios.get(`${this.store.endpoint}/currencies`).then((response) => {

        // TRASFORMO L'OGGETTO IN UN ARRAY
        let currenciesObj = response.data;
        let currenciesNames = Object.values(currenciesObj);
        let currenciesId = Object.keys(currenciesObj)

        let currencies = [];

        for (let i = 0; i < currenciesNames.length; i++) {

          let object = {
            name: currenciesNames[i],
            id: currenciesId[i]
          }

          currencies.push(object);
        }

        this.currencies = currencies;
      })


    }
  },
}
</script>
<template>
  <div class="container">
    <div class="row">
      <div class="col-12">
        <h2> From</h2>
        <h2> To</h2>
      </div>
      <div class="col-12 mt-5">
        <Input_select :currencies="currencies"></Input_select>
      </div>
    </div>
  </div>
</template>
<style lang="scss">
@use './style/generals.scss' as *;
</style>