<template>
  <PageHeader />
  <div class="pageActions">
    <span> Seus Arquivos </span>
    <BtnReadCsv :csvList="csvList" @updateList="updateList" />
  </div>
  <div v-if="csvList.length > 0">
    <q-table title="Listagem de CSV" :rows="csvList" :columns="columns" row-key="id" header-align="left" />
  </div>
  <div v-else class="emptyTable">
    <EmptyTableIcon class="tableIcon" />
    <strong>Sem tabelas importadas</strong>
    <span>Importe clicando no botão acima "+ Importar CSV" para começar</span>
  </div>
</template>

<script lang="ts">
import PageHeader from "../components/PageHeader.vue"
import BtnReadCsv from "../components/BtnReadCsv.vue"
import EmptyTableIcon from "../assets/tableIcon.svg?component"
import { format as formatFNS } from "date-fns"
import { ptBR as datePTBR } from "date-fns/locale"

type CsvList = Array<{ name: string; age: string }>

const localStorageCsvList = localStorage.getItem("csv_list")
const csvList = (localStorageCsvList ? JSON.parse(localStorageCsvList) : []) as CsvList

const columns = [
  { name: "name", label: "Nome", field: "name", align: "left" as "left", sortable: true },
  {
    name: "columns",
    label: "Número de colunas",
    field: "columns",
    align: "left" as "left",
    sortable: true,
    format: (val: string[]) => {
      return val.length
    },
  },
  { name: "rows", label: "Número de linhas", field: "rows", align: "left" as "left", sortable: true },
  { name: "isExported", label: "Exportado", field: "isExported", align: "left" as "left", sortable: true },
  {
    name: "created_at",
    label: "Criado em",
    field: "created_at",
    align: "left" as "left",
    sortable: true,
    format: (val: string) => {
      return formatFNS(new Date(val), "dd/MM/yyyy HH:mm", { locale: datePTBR })
    },
  },
]
export default {
  components: { PageHeader, BtnReadCsv, EmptyTableIcon },
  name: "CsvList",
  data() {
    return {
      csvList,
      columns,
    }
  },
  methods: {
    updateList: function (this: { csvList: CsvList }, newCsvList: CsvList) {
      this.csvList = newCsvList
    },
  },
  watch: {
    csvList(newCsvList: CsvList) {
      console.log(newCsvList)
      localStorage.setItem("csv_list", JSON.stringify(newCsvList))
    },
  },
}
</script>

<style scoped>
.pageHeader {
  color: var(--cyan-500);
  font-weight: 600;
}

.pageActions {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 1.5rem;

  margin: 1.25rem 0px;
}

.emptyTable {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  border-radius: 0.625rem;
  flex: 1;

  background: white;
}

.tableIcon {
  width: 100px;
}
</style>
