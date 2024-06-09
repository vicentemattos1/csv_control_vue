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
            :v-model="visibleColumns.includes(item.name)"
            :val="item.name"
            :label="`Esconder columns ${item.name}`"
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
