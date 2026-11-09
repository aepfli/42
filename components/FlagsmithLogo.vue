<script setup>
// Resolve against the deploy base so the logo works under /42/ on GitHub Pages too.
const src = `${import.meta.env.BASE_URL}img/flagsmith.svg`
</script>

<template>
  <img :src="src" alt="Flagsmith" />
</template>
