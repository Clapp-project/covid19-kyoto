<template>
  <v-col cols="12" md="6" class="DataCard">
    <data-table
      :title="$t('陽性患者の属性')"
      :title-id="'attributes-of-confirmed-cases'"
      :chart-data="patientsTable"
      :chart-option="{}"
      :date="patients.last_update"
      :info="sumInfoOfPatients"
      :url="'https://www.pref.kyoto.jp/kentai/news/novelcoronavirus.html#F'"
      :source="$t('オープンデータを入手')"
    />
  </v-col>
</template>

<script>
import patients from '@/data/patients.json'
import patientsSummary from '@/data/patients_summary.json'
import formatGraph from '@/utils/formatGraph'
import formatTable from '@/utils/formatTable'
import DataTable from '@/components/DataTable.vue'

export default {
  components: {
    DataTable
  },
  data() {
    // 感染者数グラフ
    const patientsGraph = formatGraph(patientsSummary.data)
    // 感染者数
    const patientsTable = formatTable(patients.data)

    const sumInfoOfPatients = {
      lText: patientsGraph[
        patientsGraph.length - 1
      ].cumulative.toLocaleString(),
      sText: this.$t('{date}の累計', {
        date: patientsGraph[patientsGraph.length - 1].label
      }),
      unit: this.$t('人')
    }

    // 陽性患者の属性 ヘッダー翻訳
    for (const header of patientsTable.headers) {
      header.text =
        header.value === '退院' ? this.$t('退院※') : this.$t(header.value)
    }
    // 陽性患者の属性 中身の翻訳
    for (const row of patientsTable.datasets) {
      row['居住地'] = this.$t(row['居住地'])
      row['性別'] = this.$t(row['性別'])
      row['退院'] = this.$t(row['退院'])

      /*
      if (row['年代'] === '10歳未満') {
        row['年代'] = this.$t('10歳未満')
      } else if (row['年代'] === '不明') {
        row['年代'] = this.$t('不明')
      } else {
        const age = row['年代'].substring(0, 2)
        row['年代'] = this.$t('{age}代', { age })
      }
      */
    }

    const data = {
      patients,
      patientsTable,
      sumInfoOfPatients
    }
    return data
  }
}
</script>
