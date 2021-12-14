<template>
  <div>
    <v-stepper v-model="e1">
      <v-stepper-header>
        <v-stepper-step :complete="e1 > 1" step="1">地域</v-stepper-step>

        <v-divider> </v-divider>

        <v-stepper-step :complete="e1 > 2" step="2">
          表示データ
        </v-stepper-step>

        <v-divider></v-divider>

        <v-stepper-step step="3">グラフ</v-stepper-step>
      </v-stepper-header>

      <v-stepper-items>
        <v-stepper-content step="1">
          <v-row>
            <v-col>
              <v-text-field
                v-model="inputPrefname"
                :rules="rules"
                :counter="10"
                label="都道府県"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <v-text-field
                v-model="inputCityname"
                :counter="30"
                label="市区町村"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-row no-gutters style="height: 60px" align="end" justify="end">
            <v-btn :color="buttonColor" @click="moveTwo()">決定</v-btn>
            <v-btn
              text
              @click="
                inputPrefname = ''
                inputCityname = ''
              "
              >クリア</v-btn
            >
          </v-row>
        </v-stepper-content>

        <v-stepper-content step="2">
          <v-card flat class="py-12">
            <v-card-text>
              <v-row align="center" justify="center">
                <v-btn-toggle mandatory>
                  <div>
                    <v-tooltip bottom>
                      <template v-slot:activator="{ on, attrs }">
                        <v-btn
                          v-bind="attrs"
                          v-on="on"
                          class="mx-8"
                          fab
                          x-large
                          @click="chartLabel = 0"
                        >
                          <v-icon x-large>mdi-human-male-female</v-icon>
                        </v-btn>
                      </template>
                      <span>総人口</span>
                    </v-tooltip>
                  </div>
                  <div>
                    <v-tooltip bottom>
                      <template v-slot:activator="{ on, attrs }">
                        <v-btn
                          v-bind="attrs"
                          v-on="on"
                          class="mx-8"
                          fab
                          x-large
                          @click="chartLabel = 3"
                        >
                          <v-icon x-large>mdi-baby</v-icon>
                        </v-btn>
                      </template>
                      <span>出生数</span>
                    </v-tooltip>
                  </div>
                  <div>
                    <v-tooltip bottom>
                      <template v-slot:activator="{ on, attrs }">
                        <v-btn
                          v-bind="attrs"
                          v-on="on"
                          class="mx-8"
                          fab
                          x-large
                          @click="chartLabel = 4"
                        >
                          <v-icon x-large>mdi-coffin</v-icon>
                        </v-btn>
                      </template>
                      <span>死亡数</span>
                    </v-tooltip>
                  </div>
                  <div>
                    <v-tooltip bottom>
                      <template v-slot:activator="{ on, attrs }">
                        <v-btn
                          v-bind="attrs"
                          v-on="on"
                          class="mx-8"
                          fab
                          x-large
                          @click="chartLabel = 1"
                        >
                          <v-icon x-large>mdi-airplane-landing</v-icon>
                        </v-btn>
                      </template>
                      <span>転入数</span>
                    </v-tooltip>
                  </div>
                  <div>
                    <v-tooltip bottom>
                      <template v-slot:activator="{ on, attrs }">
                        <v-btn
                          v-bind="attrs"
                          v-on="on"
                          class="mx-8"
                          fab
                          x-large
                          @click="chartLabel = 2"
                        >
                          <v-icon x-large>mdi-airplane-takeoff</v-icon>
                        </v-btn>
                      </template>
                      <span>転出数</span>
                    </v-tooltip>
                  </div>
                </v-btn-toggle>
              </v-row>
            </v-card-text>
          </v-card>
          <v-row no-gutters style="height: 60px" align="end" justify="end">
            <v-btn
              color="primary"
              @click="
                e1 = 3
                drawChart()
              "
            >
              決定
            </v-btn>
            <v-btn text @click="e1 = 1">ひとつ戻る</v-btn>
          </v-row>
        </v-stepper-content>

        <v-stepper-content step="3">
          <v-row justify="center" v-if="this.isSuccess">
            <GChart
              :type="chartType"
              :data="chartData"
              :options="chartOptions"
            />
          </v-row>
          <v-row no-gutters style="height: 60px" align="end" justify="end">
            <v-btn color="primary" @click="e1 = 1">最初から</v-btn>
            <v-btn text @click="e1 = 2">ひとつ戻る</v-btn>
          </v-row>
        </v-stepper-content>
      </v-stepper-items>
    </v-stepper>
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
      cityCode: '-',
      chartLabel: '1',
      chartType: 'LineChart',
      chartData: [],
      chartOptions: {
        region: 'JP',
        colors: ['#00ACC1'],
        width: 600,
        height: 300,
      },
      isSuccess: false,
      e1: '1',
      rules: [(value) => !!value || '必須項目です'],
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
          `https://opendata.resas-portal.go.jp/api/v1/population/sum/estimate?cityCode=${this.cityCode}&prefCode=${this.prefCode}`,
          {
            headers: {
              'X-API-KEY': key,
            },
          }
        )
        console.log(res)
        const resasResponce = res.data.result.data[this.chartLabel].data
        const dataset = resasResponce.map((item) => [item.year, item.value])
        this.chartData = [['年', '(人)']]
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
    moveTwo() {
      if (this.inputPrefname) {
        this.e1 = 2
      }
    },
  },
  computed: {
    buttonColor: function () {
      if (this.inputPrefname) return 'primary'
      else return 'gray'
    },
  },
}
</script>
