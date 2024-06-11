<template>
  <div>
    <q-btn @click="openFileInput" class="actionButton" outline rounded label="+ Importar CSV" />
    <input type="file" ref="fileInput" style="display: none" @change="convertToJson" />
  </div>
</template>

<script>
import csv from "csvtojson"

export default {
  methods: {
    openFileInput() {
      this.$refs.fileInput.click()
    },
    async convertToJson(event) {
      const file = event.target.files[0]
      if (!file) return

      const reader = new FileReader()
      reader.onload = async () => {
        const csvData = reader.result
        try {
          const headers = []
          let jsonData = []
          const name = event.target.files[0].name.replace(/\.[^/.]+$/, "")

          await csv()
            .preFileLine((fileLine, lineIdx) => {
              if (lineIdx === 0) {
                headers.push(...fileLine.split(","))
              }
              return fileLine
            })
            .fromString(csvData)
            .then((jsonArray) => {
              jsonData = jsonArray.map((item, index) => {
                return {
                  ...item,
                  id: index,
                }
              })
            })

          if (this.csvList.length >= 1) {
            this.$emit("updateList", [
              ...this.csvList,
              {
                id: this.csvList.length,
                name,
                rows: jsonData.length,
                columns: headers,
                isExported: false,
                created_at: new Date().toISOString(),
                content: jsonData,
              },
            ])
          } else {
            this.$emit("updateList", [
              {
                id: 0,
                name,
                rows: jsonData.length,
                columns: headers,
                isExported: false,
                created_at: new Date().toISOString(),
                content: jsonData,
              },
            ])
          }
        } catch (err) {
          console.log("Erro ao ler CSV")
        }
        this.$refs.fileInput.value = null
      }
      reader.readAsText(file)
    },
  },
  props: ["csvList"],
}
</script>

<style scoped>
.actionButton {
  text-transform: none;
  min-height: 1.5rem;
}
</style>
