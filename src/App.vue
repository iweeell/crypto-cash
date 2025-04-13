<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'

const symbol = ref('')
const price = ref(null)
const error = ref(null)
const defaultPrices = ref({})

const coins = ['BTC', 'SOL', 'XRP']


const getData = async (coin = symbol.value) => {
  if (!coin || !coin.trim()) {
    price.value = null
    return
  }

  const apiKey = 'f6b54c7f797bb3c5ec0e639b16865be2800a69451897e59825f83f1ae654f67a';
  const url = `https://min-api.cryptocompare.com/data/price?fsym=${coin.toUpperCase()}&tsyms=USD,JPY,EUR&api_key=${apiKey}`;

  error.value = false

  try {
    const response = await fetch(url);
    const data = await response.json()

    if (coin.toUpperCase() === symbol.value.toUpperCase()) {
      price.value = data.USD
      console.log(data);

    } else {
      defaultPrices.value[coin.toUpperCase()] = data.USD
    }

  } catch (err) {
    error.value = true
  }
}

onMounted(() => {

  coins.forEach(coin => {
    getData(coin)
  })

})


let intervalId

onMounted(() => {
  intervalId = setInterval(() => {
    for (const coin in defaultPrices.value) {
      const fluctuation = (Math.random() * 2 - 1).toFixed(2)
      defaultPrices.value[coin] = (Number(defaultPrices.value[coin]) + Number(fluctuation)).toFixed(2)
    }
  }, 1000)
})

onBeforeUnmount(() => {
  clearInterval(intervalId)
})
</script>


<template>
  <div class="wrapper">
    <div class="top">
      <h1 class="title">Crypto cash</h1>
      <ul>
        <li v-for="(usd, coin) in defaultPrices" :key="coin">
          {{ coin }}: ${{ usd }}
        </li>
      </ul>
      <label>
        <input type="text" v-model="symbol">
        <button @click="() => getData()">Search</button>
      </label>
      <p v-if="error">Error</p>
    </div>
    <div class="price" v-if="price">
      <span>{{ symbol.toUpperCase() }}</span>
      <span>USD: {{ price }}</span>
    </div>
  </div>

</template>


<style>
.wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
}

input {
  padding: 0.6em 1.2em;
  border-radius: 8px;
  border: none;
  background-color: #f7f7f7;
  margin-right: 15px;
  font-size: 18px;
}

.top {
  margin-bottom: 30px;
}

.price {
  display: flex;
  flex-direction: column;
  gap: 10px;
  padding: 15px;
  border-radius: 10px;
  border: 1px solid #e5e5e5;
  font-size: 20px;
  font-weight: 700;
  max-width: 200px;
}

ul {
  display: flex;
  list-style: normal;
  gap: 10px;
  margin: 0;
  padding: 15px;
}

li {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 15px;
  border: 1px dotted #e5e5e5;
  min-width: 120px;
  font-size: 20px;
  font-weight: 500;
}
</style>