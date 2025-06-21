<template>
  <q-card class="q-pa-lg q-mx-auto" style="max-width: 400px">
    <q-card-section class="row items-center q-pb-none">
      <q-icon name="trending_up" color="primary" size="md" class="q-mr-sm" />
      <div class="text-h6">Conversión de Monedas</div>
    </q-card-section>
    <q-card-section>
      <q-input v-model="amount" label="Monto a convertir" type="number" outlined dense />
      <div class="row q-col-gutter-md q-mt-md">
        <div class="col">
          <q-select
            v-model="fromCurrency"
            :options="currencies"
            label="Desde"
            outlined
            dense
            emit-value
            map-options
          />
        </div>
        <div class="col-auto flex flex-center">
          <q-btn flat round icon="swap_horiz" @click="swapCurrencies" />
        </div>
        <div class="col">
          <q-select
            v-model="toCurrency"
            :options="currencies"
            label="Hacia"
            outlined
            dense
            emit-value
            map-options
          />
        </div>
      </div>
      <q-btn class="q-mt-lg full-width" color="primary" label="Convertir" @click="convert" />
    </q-card-section>
  </q-card>
</template>

<script setup>
import { ref } from 'vue'

const amount = ref(100)
const fromCurrency = ref('USD')
const toCurrency = ref('EUR')

const currencies = [
  { label: 'USD United States Dollar', value: 'USD' },
  { label: 'EUR Euro', value: 'EUR' },
  { label: 'PEN Sol Peruano', value: 'PEN' },
  { label: 'BRL Brazilian Real', value: 'BRL' },
  { label: 'MXN Mexican Peso', value: 'MXN' },
  // Agrega más monedas si lo deseas
]

function swapCurrencies() {
  const temp = fromCurrency.value
  fromCurrency.value = toCurrency.value
  toCurrency.value = temp
}

function convert() {
  // Aquí irá la lógica de conversión
  // Por ahora solo muestra los valores en consola
  console.log(`Convertir ${amount.value} de ${fromCurrency.value} a ${toCurrency.value}`)
}
</script>

<style scoped>
.full-width {
  width: 100%;
}
</style>
