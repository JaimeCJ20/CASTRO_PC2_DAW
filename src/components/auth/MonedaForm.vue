<template>
  <div class="row justify-center items-start q-gutter-xl">
    <div class="col-12 col-md-6">
      <q-card class="q-pa-lg q-mx-auto" style="max-width: 400px">
        <q-card-section class="row items-center q-pb-none">
          <q-icon name="trending_up" color="primary" size="md" class="q-mr-sm" />
          <div class="text-h6">Conversión de Monedas</div>
        </q-card-section>
        <q-card-section>
          <q-input
            v-model="amount"
            label="Monto a convertir"
            type="number"
            outlined
            dense
            :error="amountError"
            :error-message="amountErrorMsg"
          />
          <div class="row q-col-gutter-md q-mt-md">
            <div class="col">
              <q-select
                v-model="fromCurrency"
                :options="currencyOptions"
                label="Desde"
                outlined
                dense
                emit-value
                map-options
                :loading="loadingCurrencies"
                :error="currencyError"
                :error-message="currencyErrorMsg"
              />
            </div>
            <div class="col-auto flex flex-center">
              <q-btn flat round icon="swap_horiz" @click="swapCurrencies" />
            </div>
            <div class="col">
              <q-select
                v-model="toCurrency"
                :options="currencyOptions"
                label="Hacia"
                outlined
                dense
                emit-value
                map-options
                :loading="loadingCurrencies"
                :error="currencyError"
                :error-message="currencyErrorMsg"
              />
            </div>
          </div>
          <q-btn
            class="q-mt-lg full-width"
            color="primary"
            label="Convertir"
            @click="convert"
            :loading="loadingConversion"
          />
          <q-banner v-if="conversionError" class="q-mt-md bg-red-2 text-red-10">
            {{ conversionError }}
          </q-banner>
        </q-card-section>
      </q-card>
    </div>
    <div class="col-12 col-md-4 flex flex-center">
      <q-banner
        v-if="result"
        class="bg-green-2 text-green-10 text-h6 q-pa-lg q-mt-md"
        style="min-width: 250px"
      >
        {{ result }}
      </q-banner>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const amount = ref(100)
const fromCurrency = ref(null)
const toCurrency = ref(null)
const currencyOptions = ref([])
const loadingCurrencies = ref(false)
const loadingConversion = ref(false)
const result = ref('')
const conversionError = ref('')
const amountError = ref(false)
const amountErrorMsg = ref('')
const currencyError = ref(false)
const currencyErrorMsg = ref('')

onMounted(async () => {
  await fetchCurrencies()
})

async function fetchCurrencies() {
  loadingCurrencies.value = true
  try {
    const { data } = await axios.get('https://api.frankfurter.app/currencies')
    currencyOptions.value = Object.entries(data).map(([code, name]) => ({
      label: `${code} ${name}`,
      value: code,
    }))
    // Set defaults if available
    if (!fromCurrency.value) fromCurrency.value = 'USD'
    if (!toCurrency.value) toCurrency.value = 'EUR'
  } catch {
    conversionError.value = 'No se pudieron cargar las monedas.'
  } finally {
    loadingCurrencies.value = false
  }
}

function swapCurrencies() {
  const temp = fromCurrency.value
  fromCurrency.value = toCurrency.value
  toCurrency.value = temp
}

async function convert() {
  amountError.value = false
  currencyError.value = false
  conversionError.value = ''
  result.value = ''

  if (!amount.value || isNaN(amount.value) || Number(amount.value) <= 0) {
    amountError.value = true
    amountErrorMsg.value = 'Ingrese un monto válido.'
    return
  }
  if (!fromCurrency.value || !toCurrency.value) {
    currencyError.value = true
    currencyErrorMsg.value = 'Seleccione ambas monedas.'
    return
  }
  if (fromCurrency.value === toCurrency.value) {
    conversionError.value = 'Las monedas deben ser diferentes.'
    return
  }

  loadingConversion.value = true
  try {
    const { data } = await axios.get(`https://api.frankfurter.app/latest`, {
      params: {
        amount: amount.value,
        from: fromCurrency.value,
        to: toCurrency.value,
      },
    })
    const rate = data.rates[toCurrency.value]
    result.value = `${amount.value} ${fromCurrency.value} equivalen a ${rate} ${toCurrency.value}`
  } catch {
    conversionError.value = 'No se pudo realizar la conversión.'
  } finally {
    loadingConversion.value = false
  }
}
</script>

<style scoped>
.full-width {
  width: 100%;
}
</style>
