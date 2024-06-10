<template>
  <header class="pageHeader">
    <router-link to="/">
      <h4>Início</h4>
    </router-link>
    <div v-if="url.includes('csv')" class="subPage">
      >
      <h4>Detalhe do CSV</h4>
    </div>
  </header>
</template>

<script lang="ts">
import { useRoute } from "vue-router"

export default {
  name: "PageHeader",
  setup() {
    const route = useRoute()
    return {
      url: route.path,
    }
  },
}
</script>

<style scoped>
.pageHeader {
  display: flex;
  align-items: center;
  color: var(--cyan-500);
  font-weight: 600;
  font-size: 16px;
}
.pageHeader h4 {
  color: var(--cyan-500);
  font-weight: 600;
  font-size: 18px;
}
.subPage {
  display: flex;
  margin-left: 10px;
  gap: 10px;
  color: gray;
  font-size: 14px;
  align-items: center;
}
.subPage h4 {
  font-weight: 700;
}
</style>
