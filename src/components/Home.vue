<template>
    <div class="container-fluid margins">
        <div class="row">
            <div class="col-10">
                <input type="text" class="form-control full-width" autofocus placeholder="Enter symbol..." v-model="symbol"
                    v-on:keyup.enter="search" />
            </div>
            <div class="col-2">
                <input type="button" class="btn btn-primary" value="Go" v-on:click="search" />
            </div>
        </div>

        <Quote v-bind:stock="stock" v-bind:graph="graph" />
        <div class="alert alert-danger" role="alert" v-if="error">
            <strong>Error!</strong> {{ error }}
        </div>

        <!-- <p class="small bottom">Data provided for free by
            <a href="https://iextrading.com/developer" target="_blank">IEX</a>.
            <br /> By using this application you agree to
            <a href="https://iextrading.com/api-exhibit-a" target="_blank">IEX terms of service</a>.
        </p> -->

    </div>
</template>
<script setup lang="ts">
import axios from 'axios';
import { ref } from 'vue';

//include the quote component
import Quote from './Quote.vue';

const stock = ref({});
const graph = ref({});
const error = ref('');
const symbol = ref('');
const token = '?token=<TOKEN>'

function search() {
    let url = `https://cloud.iexapis.com/stable/stock/${symbol.value}/quote${token}`;

    axios
        .get(url)
        .then(getGraphData)
        .catch(handleErrors);
};

//gets chart data from the API
function getGraphData(result: { data: any; }) {
    let gUrl = `https://cloud.iexapis.com/stable/stock/${symbol.value}/chart/1m${token}`;
    stock.value = result.data;

    axios
        .get(gUrl)
        .then((result: { data: any; }) => {
            graph.value = result.data;
        })
        .catch(handleErrors);
};
//method to handle errors
function handleErrors(err: { status: number; }) {
    if (err.status === 404) {
        error.value = "The specified symbol could not be found...";
    } else {
        error.value = "There was an error retrieving the data...";
    }
}

</script>
<style scoped>
.margins {
    padding: 40px 30px;
}

.full-width {
    width: 100%;
}

.small {
    font-size: 8px;
    color: #c0c0c0;
}

.bottom {
    position: absolute;
    bottom: 5px;
    margin: 0 auto;
}
</style>