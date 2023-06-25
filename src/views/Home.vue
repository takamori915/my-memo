<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png" /> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
    <v-data-table :headers="headers" :items="items">
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>メモ一覧</v-toolbar-title>
          <v-spacer />
          <v-btn color="primary" dark class="mb-2" @click="addItem()"
            >メモを追加</v-btn
          >
        </v-toolbar>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon small class="mr-2" @click="updateItem(item)">mdi-pencil</v-icon>
        <v-icon small @click="deleteItem(item)">mdi-delete</v-icon>
      </template>
    </v-data-table>
    <v-dialog v-model="dialogDelete" max-width="500px">
      <v-card>
        <v-card-title>この項目を削除してもよろしいですか？</v-card-title>
        <v-card-actions>
          <v-spacer />
          <v-btn color="blue darken-1" text @click="closeDialogDelete"
            >キャンセル</v-btn
          >
          <v-btn color="blue darken-1" text @click="deleteItemAction"
            >削除</v-btn
          >
          <v-spacer />
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogEdit" max-width="500px">
      <v-card>
        <v-card-title>
          <span class="text-h5">{{ dialogTitle }}</span>
        </v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12">
              <v-text-field
                v-model="editItem.title"
                label="タイトル"
              ></v-text-field>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="blue darken-1" text @click="closeDialogEdit"
            >キャンセル</v-btn
          >
          <v-btn color="blue darken-1" text @click="saveDialogEdit">保存</v-btn>
          <v-spacer />
        </v-card-actions>
      </v-card>
    </v-dialog>
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
    dialogEdit: false,
    dialogDelete: false,
    editIndex: -1,
    editItem: {},
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
        width: "250px",
      },
      {
        text: "更新日",
        value: "updatedAt",
        align: "center",
        width: "250px",
      },
      {
        text: "",
        value: "actions",
        align: "center",
      },
    ],
  }),
  computed: {
    dialogTitle() {
      return this.editIndex === -1 ? "メモの追加" : "メモの更新";
    },
  },
  async mounted() {
    await this.getItems();
  },
  methods: {
    async getItems() {
      this.isLoading = true;
      const filter = "filters=deletedAt[not_exists]";
      const response = await axios
        .get("https://takamori-c.microcms.io/api/v1/favorites?" + filter, {
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
          self.errors = e;
        })
        .finally(() => {
          self.isLoading = false;
        });
      this.items = response.data.contents;
      return response;
    },
    async postItems() {
      this.isLoading = true;
      const self = this;
      await axios
        .post(
          "https://takamori-c.microcms.io/api/v1/favorites",
          this.editItem,
          {
            headers: {
              "X-MICROCMS-API-KEY": process.env.VUE_APP_X_MICROCMS_API_KEY,
            },
          }
        )
        .catch(function (e) {
          self.errors = e;
        })
        .finally(() => {
          self.isLoading = false;
        });
    },
    async patchItems() {
      this.isLoading = true;
      const self = this;
      const params = {
        title: this.editItem.title,
        content: this.editItem.content,
        category: this.editItem.category,
        categoryDetail: this.editItem.categoryDetail,
        deletedAt: this.editItem.deletedAt,
      };
      await axios
        .patch(
          "https://takamori-c.microcms.io/api/v1/favorites/" + self.editItem.id,
          params,
          {
            headers: {
              "X-MICROCMS-API-KEY": process.env.VUE_APP_X_MICROCMS_API_KEY,
            },
          }
        )
        .catch(function (e) {
          self.errors = e;
        })
        .finally(() => {
          self.isLoading = false;
        });
      this.isLoading = false;
    },
    addItem() {
      this.editIndex = -1;
      this.editItem = {
        title: "test",
        content: "",
        category: ["マニュアル"],
        categoryDetail: ["VUE"],
        deletedAt: null,
      };
      this.dialogEdit = true;
    },
    updateItem(item) {
      this.editIndex = this.items.indexOf(item);
      this.editItem = Object.assign({}, item);
      this.dialogEdit = true;
    },
    deleteItem(item) {
      this.editIndex = this.items.indexOf(item);
      this.editItem = Object.assign({}, item);
      this.editItem.deletedAt = new Date();
      this.dialogDelete = true;
    },
    closeDialogDelete() {
      this.dialogDelete = false;
    },
    async deleteItemAction() {
      this.items.splice(this.editIndex, 1);
      await this.patchItems();
      this.closeDialogDelete();
    },
    closeDialogEdit() {
      this.dialogEdit = false;
    },
    async saveDialogEdit() {
      if (this.editIndex > -1) {
        Object.assign(this.items[this.editIndex], this.editItem);
        await this.patchItems();
      } else {
        this.items.push(this.editItem);
        await this.postItems();
      }
      await this.getItems();
      this.closeDialogEdit();
    },
  },
};
</script>
