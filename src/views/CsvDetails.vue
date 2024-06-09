<script lang="ts">
import PageHeader from "../components/PageHeader.vue"
import BtnReadCsv from "../components/BtnReadCsv.vue"
export default {
  name: "CsvDetail",
  components: { PageHeader, BtnReadCsv },
}
</script>

<template>
  <PageHeader />
  <BtnReadCsv />
</template>

<style scoped>
.CsvDetail {
  color: var(--cyan-500);
  font-weight: 600;
}
</style>
