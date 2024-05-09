<script>
import axios from 'axios';
import { store } from '../../store';
export default {
    name: 'Input_select',
    props: {
        currencies: Array,
    },
    data() {
        return {
            store,
            currency1: 'EUR',
            currency2: 'USD',
            lastFrom: 'value1',
            value1: 1,
            value2: 1,
            error: null,
            chartData: null,
            chartCategories: null,
            series: [],
            options: {
                chart: {
                    id: 'vuechart-example'
                },
                xaxis: {
                    categories: [],
                },
                colors: ['#ffffff', '#ffffff', '#ffffff', '#ffffff', '#ffffff'],
            },
        }
    },
    mounted() {
        this.getValues('value1');
    },
    created() {
    },
    watch: {
        currency1() {
            this.getValues(this.lastFrom);
        },
        currency2() {
            this.getValues(this.lastFrom);
        },
        // series() {
        //     this.getChartInfo(this.currency1, this.currency2)
        // }

    },
    methods: {
        getValues(from) {
            // CONTROLLO SUI VALUES PER NON FARE CHIAMATE INUTILI
            if (this.value1 != 0 && this.value2 != 0 && this.value1 != null && this.value2 != null) {

                if (from == 'value1') {
                    this.lastFrom = 'value1';

                    axios.get(`${this.store.endpoint}/latest?amount=${this.value1}&from=${this.currency1}&to=${this.currency2}`).then((response) => {

                        // RECUPERO IL RATE CHANGE
                        let result = response.data.rates;
                        // RECUPERO IL VALORE IN STRINGA
                        let str = Object.values(result);
                        // E LO APPLICO ALL'ALTRO INPUT
                        this.value2 = str;

                        // ASSEGNO I VALORI CORRETTI ALLE VARIABILI CHE MOSTRANO IL CAMBIO DELLA VALUTA
                        this.store.fromChange = this.value1 + ' ' + this.currency1;
                        this.store.toChange = this.value2 + ' ' + this.currency2;

                        this.getChartInfo(this.currency1, this.currency2);
                    }).catch(error => {
                        console.error(error)
                    })

                } else if (from == 'value2') {
                    this.lastFrom = 'value2';

                    axios.get(`${this.store.endpoint}/latest?amount=${this.value2}&from=${this.currency2}&to=${this.currency1}`).then((response) => {

                        // RECUPERO IL RATE CHANGE
                        let result = response.data.rates;
                        // RECUPERO IL VALORE IN STRINGA
                        let str = Object.values(result);
                        // E LO APPLICO ALL'ALTRO INPUT
                        this.value1 = str;

                        // ASSEGNO I VALORI CORRETTI ALLE VARIABILI CHE MOSTRANO IL CAMBIO DELLA VALUTA
                        this.store.fromChange = this.value2 + this.currency2;
                        this.store.toChange = this.value1 + this.currency1;

                        this.getChartInfo(this.currency2, this.currency1);
                    }).catch(error => {
                        console.error(error)
                    })

                }
            }
        },
        getChartInfo(from, to) {
            axios.get(`${this.store.endpoint}/2020-01-01..2024-05-01?&from=${from}&to=${to}`).then((response) => {
                let result = response.data.rates;

                // Pulisci dai valori precedenti
                this.chartCategories = Object.keys(result);
                this.chartData = Object.values(result).map(elem => elem.USD);

                // Aggiorna le opzioni del grafico con i nuovi dati
                this.options.xaxis.categories = this.chartCategories;

                // Assicurati che la serie contenga almeno un elemento prima di aggiornarla
                if (!this.series || this.series.length === 0) {
                    this.series.push({ name: 'series-1', data: [] });
                }

                // Aggiorna la serie dati solo se ci sono dati validi
                if (this.chartData && this.chartData.length > 0) {
                    this.series[0].data = this.chartData;
                } else {
                    console.error("Nessun dato valido per la serie del grafico");
                }
            }).catch(error => {
                console.error(error)
            })
        }
    }
}

</script>
<template>
    <div class="container">
        <div class="row justify-content-evenly">


            <!-- PRIMA INPUT CON SELECT-->
            <div class="col-12">
                <div class="row justify-content-center">
                    <div class="col-5 m-1">
                        <div>
                            <label for="value1" class="form-label mb-1">Value</label>
                            <input type="text" class="form-control" id="value1" v-model="value1"
                                @keyup="getValues('value1')">
                        </div>

                    </div>

                    <div class="col-5 m-1">
                        <label for="currency1" class="form-label mb-1">Currency</label>
                        <select class="form-select" name="currency1" id="currency1" v-model="currency1">
                            <option v-show="currency.id != currency2" v-for="currency in currencies"
                                :value="currency.id" :id="currency.id">{{
                                    currency.name
                                }}</option>
                        </select>
                    </div>
                </div>
            </div>


            <!-- SECONDA INPUT CON SELECT -->
            <div class="col-12">
                <div class="row justify-content-center">
                    <div class="col-5 m-1">
                        <div>
                            <label for="value2" class="form-label mb-1">Value</label>
                            <input type="text" class="form-control" id="value2" v-model="value2"
                                @keyup="getValues('value2')">
                        </div>

                    </div>

                    <div class="col-5 m-1">
                        <label for="currency2" class="form-label mb-1">Currency</label>
                        <select class="form-select" name="currency2" id="currency2" v-model="currency2">
                            <option v-show="currency.id != currency1" v-for="currency in currencies"
                                :value="currency.id" :id="currency.id">{{
                                    currency.name
                                }}</option>
                        </select>
                    </div>
                </div>
            </div>

            <div class="col-12 mt-5">
                <div>
                    <apexchart width="100%" type="line" :options="options" :series="series"></apexchart>
                </div>
            </div>

        </div>
    </div>
</template>
<style scoped lang="scss"></style>