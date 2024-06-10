<template>
  <q-btn-dropdown class="dropdownButton" outline rounded label="Colunas">
    <q-list>
      <q-item clickable v-close-popup @click="showAllColumns">
        <q-item-section>
          <q-item-label>Mostrar todas as colunas</q-item-label>
        </q-item-section>
      </q-item>
      <q-item clickable v-close-popup @click="hideAllColumns">
        <q-item-section>
          <q-item-label>Esconder todas as colunas</q-item-label>
        </q-item-section>
      </q-item>
      <q-item clickable v-close-popup v-for="(item, index) in columns" :key="index" @click="hideColumn(item.name)">
        <q-item-section>
          <q-toggle
            :model-value="visibleColumns.includes(item.name)"
            @click="hideColumn(item.name, visibleColumns.includes(item.name))"
            :label="`Esconder coluna ${item.name}`"
          />
        </q-item-section>
      </q-item>
    </q-list>
  </q-btn-dropdown>
</template>

<script>
export default {
  name: "BtnDropdown",
  methods: {
    handleColumnHide(e) {
      console.log(e)
    },
    hideAllColumns() {
      this.$emit("updateVisibleColumns", [])
    },
    showAllColumns() {
      this.$emit("updateVisibleColumns", this.columns)
    },
    hideColumn(item, checked) {
      let updatedVisibleColumns = []
      if (checked) {
        updatedVisibleColumns = this.visibleColumns.filter((column) => column !== item)
      } else {
        updatedVisibleColumns = [...this.visibleColumns, item]
      }
      this.$emit("updateVisibleColumns", updatedVisibleColumns)
    },
  },
  props: ["columns", "visibleColumns", "updateVisibleColumns"],
}
</script>

<style scoped>
.dropdownButton {
  text-transform: none;
  min-height: 1.5rem;
}
</style>
