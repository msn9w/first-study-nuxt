<template>
  <div>
    <v-row>
      <v-text-field
        v-model="inputPrefname"
        :counter="10"
        label="都道府県"
      ></v-text-field>
      <v-text-field
        class="ml-6"
        v-model="inputCityname"
        :counter="30"
        label="市区町村"
      ></v-text-field>
    </v-row>
    <div v-if="isSuccess">
      <GChart :type="chartType" :data="chartData" />
    </div>
    <v-row justify="end">
      <v-btn @click="drawChart()">取得</v-btn>
    </v-row>
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
      inputPrefname: '',
      inputCityname: '',
      prefCode: '',
      cityCode: '',
      chartType: 'ColumnChart',
      chartData: [],
      isSuccess: false,
    }
  },
  methods: {
    async getPrefCityCode() {
      const key = '93MXg0CmseWkxXaDpqRE7IEAksiAWZDEMq6kRNB8'
      const prefcodeData = await this.$axios.get(
        `https://opendata.resas-portal.go.jp/api/v1/prefectures`,
        {
          headers: {
            'X-API-KEY': key,
          },
        }
      )
      const resPref = prefcodeData.data.result
      for (let i = 0; i < resPref.length; i++) {
        if (resPref[i].prefName === this.inputPrefname) {
          this.prefCode = resPref[i].prefCode
        }
      }
      const citycodeData = await this.$axios.get(
        `https://opendata.resas-portal.go.jp/api/v1/cities?prefCode=${this.prefCode}`,
        {
          headers: {
            'X-API-KEY': key,
          },
        }
      )
      const resCity = citycodeData.data.result
      for (let i = 0; i < resCity.length; i++) {
        if (resCity[i].cityName === this.inputCityname) {
          this.cityCode = resCity[i].cityCode
        }
      }
    },
    async drawChart() {
      const key = '93MXg0CmseWkxXaDpqRE7IEAksiAWZDEMq6kRNB8'
      await this.getPrefCityCode()
      try {
        const res = await this.$axios.get(
          `https://opendata.resas-portal.go.jp/api/v1/population/composition/perYear?cityCode=${this.cityCode}&prefCode=${this.prefCode}`,
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
