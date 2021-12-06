<template>
  <div>
    <div v-if="isSuccess">
      <GChart :type="chartType" :data="chartData" />
    </div>
    <v-btn @click="drawChart()">取得</v-btn>
  </div>
</template>

<script>
import { GChart } from 'vue-google-charts'
export default {
  components: {
    GChart,
  },
  data() {
    return {
      chartType: 'ColumnChart',
      chartData: [],
      isSuccess: false,
    }
  },
  methods: {
    async drawChart() {
      const key = '93MXg0CmseWkxXaDpqRE7IEAksiAWZDEMq6kRNB8'
      const cityCode = 13201
      const prefCode = 13
      try {
        const res = await this.$axios.get(
          `https://opendata.resas-portal.go.jp/api/v1/population/composition/perYear?cityCode=${cityCode}&prefCode=${prefCode}`,
          {
            headers: {
              'X-API-KEY': key,
            },
          }
        )
        const resasResponce = res.data.result.data[0].data
        const dataset = resasResponce.map((item) => [item.year, item.value])
        this.chartData = [['年', '人口']]
        for (let i = 0; i < dataset.length; i++) {
          this.chartData.push(dataset[i])
        }
        console.log(resasResponce)
        this.isSuccess = true
      } catch (error) {
        console.log('エラーが起こりました')
        this.isSuccess = false
      }
    },
  },
}
</script>
