<script setup>

import { ref } from 'vue'

const symbol = ref('')
const price = ref(null)
const loading = ref(false)
const error = ref(null)

const fetchPrice = async () => {
  if (!symbol.value) {
    error.value = 'Введите символ монеты (например BTC)'
    return
  }

  loading.value = true
  error.value = null
  price.value = null

  const apiKey = 'f6b54c7f797bb3c5ec0e639b16865be2800a69451897e59825f83f1ae654f67a';
  const url = `https://min-api.cryptocompare.com/data/price?fsym=${symbol.value.toUpperCase()}&tsyms=USD,JPY,EUR&api_key=${apiKey}`;

  try {
    const response = await fetch(url)
    const data = await response.json()

    if (data.Response === 'Error') {
      throw new Error(data.Message)
    }

    price.value = data
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }

}
</script>

<template>
  <h1>Crypto</h1>
  <!-- <input type="text">
  <button>Search</button>
  <div>
    <p class="crypto__name">BTC</p>
    <span class="total__price">88888 usd</span>
  </div> -->
  <div class="crypto-checker">
    <h1>🔍 Курс криптовалюты</h1>

    <input v-model="symbol" placeholder="Введите тикер, например BTC" />
    <button @click="fetchPrice">Узнать цену</button>

    <div v-if="loading">Загрузка...</div>
    <div v-else-if="error">{{ error }}</div>

    <div v-if="price">
      <h2>Цена для {{ symbol.toUpperCase() }}:</h2>
      <ul>
        <li v-for="(value, currency) in price" :key="currency">
          {{ currency }}: {{ value }}
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped></style>
