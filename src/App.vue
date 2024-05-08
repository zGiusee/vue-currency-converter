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

  },
  created() {
    this.getCurrencies();
  },
  methods: {

    getCurrencies() {
      axios.get(`${this.store.endpoint}/currencies`).then((response) => {

        // TRASFORMO L'OGGETTO IN UN ARRAY
        let currenciesObj = response.data;
        let currenciesNames = Object.values(currenciesObj);
        let currenciesId = Object.keys(currenciesObj);

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
  <main class="d-flex align-items-center">
    <div class="container">
      <div class="row justify-content-center align-items-center">

        <div class="col-6">
          <div class="converter-container">

            <div class="row justify-content-center py-5">
              <div class="col-12 text-center">
                <h1 class=" fw-bold">
                  Currency converter
                </h1>
              </div>

              <div class="col-10 my-3 ">
                <h4> From {{ store.fromChange }}</h4>
                <h4 class="my-1"> To {{ store.toChange }}</h4>
              </div>

              <div class="col-12">
                <Input_select :currencies="currencies"></Input_select>
              </div>
            </div>


          </div>
        </div>


      </div>
    </div>
  </main>
</template>
<style lang="scss">
@use './style/generals.scss' as *;

main {
  background: linear-gradient(90deg, #FDBB2D 0%, #3A1C71 100%);
  min-height: 100vh;

  .converter-container {
    border-radius: 24px;
    border: 2px solid;
    padding: 10px;
    box-shadow: 10px 15px;
    color: white;
    background-color: rgba(255, 255, 255, 0.219);


  }

}
</style>