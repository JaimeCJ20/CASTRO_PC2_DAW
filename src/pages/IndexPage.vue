<template>
  <q-page class="flex flex-center">
    <div>
      <img
        alt="Quasar logo"
        src="~assets/quasar-logo-vertical.svg"
        style="width: 200px; height: 200px"
      />
      <MonedaForm class="q-mt-xl" />
    </div>
  </q-page>
</template>

<script setup>
import MonedaForm from 'components/auth/MonedaForm.vue'
</script>
