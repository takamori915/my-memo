<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png" /> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
    <v-data-table :headers="headers" :items="items" />
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
        text: "完了",
        value: "complete",
        algin: "center",
      },
      {
        text: "タイトル",
        value: "title",
        algin: "left",
      },
      {
        text: "カテゴリ",
        value: "categoryView",
        algin: "left",
      },
      {
        text: "登録日",
        value: "createdAt",
        algin: "center",
      },
      {
        text: "",
        value: "action",
        algin: "center",
      },
    ],
  }),
  async mounted() {
    this.isLoading = true;
    const response = await axios.get(
      "https://takamori-c.microcms.io/api/v1/favorites", {
        headers: {
          "X-MICROCMS-API-KEY": process.env.VUE_APP_X_MICROCMS_API_KEY,
        },
      })
      .then((res) => {
        res.data.contents.forEach((data) => {
          data.categoryView = data.category + " " + data.categoryDetail;
        });
      })
      .error((e) => {
        this.errors = e;
      })
      .finally(() => {
        this.isLoading = false;
      });
    this.items = response.data.contents;
    console.log("this.items****");
    console.log(this.items);
    return response;
  },
};
</script>
