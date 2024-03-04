<template>
  <v-app>
    <v-main>
      <UtilCalc />
      <analytics />
    </v-main>
  </v-app>
</template>

<script setup>
import UtilCalc from './components/UtilCalc.vue';
import { inject } from "@vercel/analytics"
//
</script>
