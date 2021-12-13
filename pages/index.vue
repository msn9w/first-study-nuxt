<template>
  <v-row justify="center" align="center">
    <v-col>
      <v-container>
        <v-row>
          <v-col>
            <v-text-field
              v-model="inputName"
              :counter="30"
              label="user name"
              append-outer-icon="mdi-magnify"
              @click:append-outer="searchGithubAccount()"
            >
            </v-text-field>
          </v-col>
        </v-row>
      </v-container>

      <v-card class="mx-auto mt-4" max-width="344" outlined>
        <v-list-item three-line>
          <v-list-item-content>
            <div class="text-overline mb-4">OVERLINE</div>
            <v-list-item-title class="text-h5 mb-1">
              {{ anotherAccountName }}
            </v-list-item-title>
            <v-list-item-subtitle>
              {{ anotherProfileText }}
            </v-list-item-subtitle>
          </v-list-item-content>
          <v-list-item-avatar tile size="80" color="grey"
            ><v-img :src="anotherImgSrc"></v-img
          ></v-list-item-avatar>
        </v-list-item>
      </v-card>

      <v-card class="mx-auto mt-4" max-width="344" outlined>
        <v-list-item three-line>
          <v-list-item-content>
            <div class="text-overline mb-4">OVERLINE</div>
            <v-list-item-title class="text-h5 mb-1">
              {{ accountName }}
            </v-list-item-title>
            <v-list-item-subtitle>
              {{ profileText }}
            </v-list-item-subtitle>
          </v-list-item-content>
          <v-list-item-avatar tile size="80" color="grey"
            ><v-img :src="imgSrc"></v-img
          ></v-list-item-avatar>
        </v-list-item>
        <v-card-actions>
          <v-btn @click="changeName()" outlined rounded text> 名前変更 </v-btn>
          <v-btn @click="getGithubData()">Githubと連携</v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  data() {
    return {
      accountName: '',
      profileText: '',
      imgSrc: '',
      inputName: '',
      anotherAccountName: '',
      anotherProfileText: '',
      anotherImgSrc: '',
    }
  },
  methods: {
    changeName() {
      this.accountName = '名前変更しました'
    },
    async getGithubData() {
      const githubUserName = `msn9w`
      let res = await this.$axios.get(
        `https://api.github.com/users/${githubUserName}`
      )
      this.accountName = res.data.login
      this.profileText = res.data.bio
      this.imgSrc = res.data.avatar_url
    },
    async searchGithubAccount() {
      const githubUserName = this.inputName
      let res = await this.$axios.get(
        `https://api.github.com/users/${githubUserName}`
      )
      this.anotherAccountName = res.data.login
      this.anotherProfileText = res.data.bio
      this.anotherImgSrc = res.data.avatar_url
    },
  },
}
</script>
