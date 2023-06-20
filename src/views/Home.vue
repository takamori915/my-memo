<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png" /> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
    <v-data-table :headers="headers" :items="items">
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>メモ一覧</v-toolbar-title>
        </v-toolbar>
      </template>
    </v-data-table>
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from "@/components/HelloWorld.vue";

import axios from "axios";
export default {
  name: "Home",
  components: {
    // HelloWorld,
  },
  data: () => ({
    isLoading: false,
    items: [],
    errors: [],
    headers: [
      {
        text: "ステータス",
        value: "completeView",
        align: "center",
        width: "120px",
      },
      {
        text: "カテゴリ",
        value: "categoryView",
        align: "left",
        width: "200px",
      },
      {
        text: "タイトル",
        value: "title",
        align: "left",
        width: "400px",
      },
      {
        text: "登録日",
        value: "createdAt",
        align: "center",
        width: "300px",
      },
      {
        text: "",
        value: "action",
        align: "center",
      },
    ],
  }),
  async mounted() {
    this.isLoading = true;
    const response = await axios
      .get("https://takamori-c.microcms.io/api/v1/favorites", {
        headers: {
          "X-MICROCMS-API-KEY": process.env.VUE_APP_X_MICROCMS_API_KEY,
        },
      })
      .then((res) => {
        res.data.contents.forEach((data) => {
          data.categoryView = data.category + " - " + data.categoryDetail;
          data.completeView = data.complete ? "済" : "";
        });
        return res;
      })
      .catch((e) => {
        this.errors = e;
      })
      .finally(() => {
        this.isLoading = false;
      });
    this.items = response.data.contents;
    return response;
  },
};
</script>
