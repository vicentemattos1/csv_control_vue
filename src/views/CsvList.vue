<template>
  <PageHeader />
  <div class="pageActions">
    <span> Seus Arquivos </span>
    <BtnReadCsv :csvList="csvList" @updateList="updateList" />
  </div>
  <div v-if="csvList.length > 0">
    <q-table
      title="Listagem de CSV"
      :rows="csvList"
      :columns="columns"
      row-key="id"
      header-align="left"
      @row-click="onRowClick"
    >
      <template v-slot:body-cell-download="props">
        <q-td :props="props">
          <q-btn flat @click.stop="downloadCsv(props.row.id)" class="downloadIcon"><DownloadIcon /></q-btn>
        </q-td>
      </template>
    </q-table>
  </div>
  <div v-else class="emptyTable">
    <EmptyTableIcon class="tableIcon" />
    <strong>Sem tabelas importadas</strong>
    <span>Importe clicando no botão acima "+ Importar CSV" para começar</span>
  </div>
</template>

<script lang="ts">
import EmptyTableIcon from "../assets/tableIcon.svg?component"
import DownloadIcon from "../assets/downloadIcon.svg?component"
import { format as formatFNS } from "date-fns"
import { ptBR as datePTBR } from "date-fns/locale"
import { exportToExcel } from "../utils/exportToXlsx"
import { useRouter } from "vue-router"
import router from "../route"

type CsvList = Array<{
  id: string
  name: string
  columns: string
  rows: string
  isExported: boolean
  created_at: string
  content: Array<any>
}>
type Column = {
  name: string
  label: string
  field: string | ((row: any) => any)
  required?: boolean
  align?: "left" | "right" | "center"
  sortable?: boolean
  sort?: (a: any, b: any, rowA: any, rowB: any) => number
  format?: (val: any, row: any) => any
}

let csvList = [] as CsvList
let columns = [] as Column[]

export default {
  components: { EmptyTableIcon, DownloadIcon },
  name: "CsvList",
  data() {
    return {
      csvList,
      columns,
    }
  },
  setup() {
    const router = useRouter()
    return {
      router,
    }
  },
  mounted() {
    const localStorageCsvList = localStorage.getItem("csv_list")
    this.csvList = (localStorageCsvList ? JSON.parse(localStorageCsvList) : []) as CsvList

    this.columns = [
      { name: "name", label: "Nome", field: "name", align: "left", sortable: true },
      {
        name: "columns",
        label: "Número de colunas",
        field: "columns",
        align: "left",
        sortable: true,
        format: (val: string[]) => {
          return val.length
        },
      },
      { name: "rows", label: "Número de linhas", field: "rows", align: "left", sortable: true },
      {
        name: "isExported",
        label: "Exportado",
        field: "isExported",
        align: "left",
        sortable: true,
        format: (val: boolean) => {
          return val ? "Sim" : "Não"
        },
      },
      {
        name: "created_at",
        label: "Criado em",
        field: "created_at",
        align: "left",
        sortable: true,
        format: (val: string) => {
          return formatFNS(new Date(val), "dd/MM/yyyy HH:mm", { locale: datePTBR })
        },
      },
      { name: "download", label: "Download", field: "", align: "left" },
    ]
  },
  methods: {
    updateList: function (newCsvList: CsvList) {
      this.csvList = newCsvList
      localStorage.setItem("csv_list", JSON.stringify(newCsvList))
    },
    onRowClick(_: any, row: { id: string }) {
      router.push(`/csv/${row.id}`)
    },
    downloadCsv(id: string) {
      const itemToDownload = this.csvList.find((item) => item.id == id)
      if (itemToDownload) {
        try {
          exportToExcel(itemToDownload.content, itemToDownload.name)
          const newCsvList = this.csvList.map((item) => {
            if (item.id == id) {
              return {
                ...item,
                isExported: true,
              }
            }
            return item
          })
          this.updateList(newCsvList)
        } catch (err) {
          console.log("Erro ao exportar")
        }
      }
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

.downloadIcon {
  border: none;
  outline: none;
  box-shadow: none;
}
.downloadIcon svg {
  width: 25px;
}
</style>
