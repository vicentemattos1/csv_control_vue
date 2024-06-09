<template>
  <PageHeader />
  <div class="pageActions">
    <BtnDropdown :columns="columns" :visibleColumns="visibleColumns" @updateVisibleColumns="updateVisibleColumns" />
    <div class="actionButtons">
      <q-btn @click="confirm = true" class="actionButton" outline rounded label="Exportar tabela" />
      <q-btn to="/" class="actionButton" outline rounded label="<- Voltar" />
    </div>
  </div>
  <q-table
    v-if="csvDetail"
    :rows="csvDetail.content"
    :columns="columns"
    row-key="name"
    header-align="left"
    selection="multiple"
    v-model:selected="selected"
    :visibleColumns="visibleColumns"
  />
  <q-dialog v-model="confirm" persistent class="confirm-dialog">
    <q-card>
      <q-card-section class="row items-center">
        <span class="q-ml-sm">Deseja exportar e baixar a tabela?</span>
      </q-card-section>

      <q-card-actions align="right">
        <q-btn outline rounded label="CANCELAR" color="black" :size="'sm'" class="custom-font-weight" v-close-popup />
        <q-btn rounded label="SIM" color="black" :size="'sm'" v-close-popup @click="exportTable(selected)" />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script lang="ts">
import { ref } from "vue"
import { useRoute } from "vue-router"
import { exportToExcel } from "../utils/exportToXlsx"

type CsvList = Array<{
  id: string
  name: string
  columns: string[]
  rows: string
  isExported: string
  created_at: string
  content: Array<any>
}>

const csvDetail = null as { id: string; name: string; content: Array<any> } | null
const columns = [] as Array<any>
const visibleColumns = [] as string[]

export default {
  name: "CsvDetail",
  components: {},
  data() {
    return {
      csvDetail,
      columns,
      visibleColumns,
    }
  },
  setup() {
    const route = useRoute()

    const selected = ref([])
    const confirm = ref(false)

    return {
      selected,
      confirm,
      csvId: route.params.id,
    }
  },
  mounted() {
    const localStorageCsvList = localStorage.getItem("csv_list")
    const csvList = (localStorageCsvList ? JSON.parse(localStorageCsvList) : []) as CsvList
    const csvItem = csvList.find((item) => item.id == this.csvId)
    if (csvItem) {
      this.csvDetail = csvItem
      for (const column of csvItem.columns) {
        this.columns.push({ name: column, label: column, field: column, align: "left", sortable: true })
      }
      this.visibleColumns = this.columns.map((item) => item.name)
    }
  },
  methods: {
    updateVisibleColumns(newVisibleColumns: string[]) {
      this.visibleColumns = newVisibleColumns
    },
    exportTable(selected: Array<any>) {
      exportToExcel(selected, this.csvDetail?.name)
    },
  },
}
</script>

<style scoped>
.pageActions {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 20px 0;
}

.actionButtons {
  display: flex;
  gap: 10px;
}
.actionButton {
  text-transform: none;
  min-height: 1.5rem;
}

.confirm-dialog .q-card {
  border-radius: 20px;
}

.custom-font-weight {
  font-weight: 700;
}
</style>
