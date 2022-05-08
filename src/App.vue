<template>
  <router-view />
</template>

<script>
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'App',
  prod: 'localhost:8081/#/',
  // prod: 'localhost:8080/competition/#/',
  // host: 'localhost:8080/competition-1.0/'
  host: 'localhost:8080/competition'
})
</script>
