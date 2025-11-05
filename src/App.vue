<script setup>
import HandleCalendar from '@/components/HandleCalendar.vue'
import { LANGUAGES } from './constants'

function showDate(date) {
  alert(date)
}
</script>

<template>
  <HandleCalendar @selected-date="showDate" :language="LANGUAGES.RU" />
</template>

<style>
html,
body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>
