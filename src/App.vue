<template>
  <v-app>
    <aplication-bar />
    <navigation-bar />
    <default-view />
    <footer-bar />
  </v-app>
</template>

<script setup>
  import aplicationBar from '@/layouts/AppBar.vue'
  import navigationBar from '@/layouts/NavBar.vue'
  import DefaultView from '@/layouts/View.vue'
  import footerBar from '@/layouts/Footer.vue'
</script>

