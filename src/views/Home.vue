<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png" /> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
    <v-data-table
      :headers="headers"
      :items="items"
      hide-default-header
      disable-sort
      @click:row="updateItem"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>メモ一覧</v-toolbar-title>
          <v-spacer />
          <v-btn color="primary" dark class="mb-2" @click="addItem()"
            >メモを追加</v-btn
          >
        </v-toolbar>
        <v-divider />
      </template>
      <template v-slot:[`item.title`]="{ item }">
        <v-row>
          <v-col cols="10" sm="11" class="text-left">
            {{ item.title }}
          </v-col>
          <v-col cols="2" sm="1" class="text-center" @click.stop>
            <v-icon small @click="deleteItem(item)">mdi-delete</v-icon>
          </v-col>
        </v-row>
      </template>
      <template v-slot:[`item.actions`]="{ item }">
        <v-row>
          <v-col class="text-center">
            <v-icon small class="mr-2" @click="updateItem(item)"
              >mdi-pencil</v-icon
            >
          </v-col>
          <v-col cols="6">
            <v-icon small @click="deleteItem(item)">mdi-delete</v-icon>
          </v-col>
        </v-row>
      </template>
    </v-data-table>
    <v-divider />
    <v-dialog v-model="dialogDelete" max-width="500px">
      <v-card>
        <v-card-title>この項目を削除してもよろしいですか？</v-card-title>
        <v-card-actions>
          <v-spacer />
          <v-btn color="red darken-1" text @click="deleteItemAction"
            >削除</v-btn
          >
          <v-btn color="blue darken-1" text @click="closeDialogDelete"
            >キャンセル</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogEdit" max-width="100%">
      <v-card>
        <v-card-title>
          <span class="text-h5">{{ dialogTitle }}</span>
          <v-spacer />
          <!-- <v-btn
            v-if="editIndex > -1"
            color="red darken-1"
            text
            small
            class="pr-8"
            @click="deleteDialogEdit"
            ><v-icon>mdi-delete</v-icon></v-btn
          > -->
          <v-btn
            color="blue darken-1"
            class="px-1"
            text
            small
            @click="saveDialogEdit"
            ><v-icon>mdi-content-save</v-icon></v-btn
          >
          <v-btn
            color="blue darken-1"
            class="px-1"
            text
            small
            @click="closeDialogEdit"
            ><v-icon>mdi-close-outline</v-icon></v-btn
          >
        </v-card-title>
        <v-card-text>
          <v-row dense>
            <v-col cols="12">
              <v-text-field
                v-model="editItem.title"
                label="タイトル"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <mavon-editor
                v-model="editItem.contentHtml"
                style="height: 600px"
                language="ja"
                @change="changeEditor"
                defaultOpen="preview"
                :toolbars="toolbars"
                :subfield="false"
              ></mavon-editor>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6">
              <v-select
                v-model="editItem.category"
                label="カテゴリ"
                item-text="name"
                item-value="name"
                :items="categoryItems"
              ></v-select>
            </v-col>
            <v-col cols="12" sm="6">
              <v-select
                v-model="editItem.categoryDetail"
                label="カテゴリ詳細"
                item-text="name"
                item-value="name"
                :items="categoryDetailItems"
              ></v-select>
            </v-col>
          </v-row>
          <!-- <v-row>
            <v-col>
              {{ mdText }}
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <div v-html="htmlText" />
            </v-col>
          </v-row> -->
        </v-card-text>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from "@/components/HelloWorld.vue";

import Vue from "vue";
import axios from "axios";
import constants from "../common/constants";
import mavonEditor from "mavon-editor";
import "mavon-editor/dist/css/index.css";
Vue.use(mavonEditor);

export default {
  name: "Home",
  components: {
    // HelloWorld,
    //mavonEditor,
  },
  data: () => ({
    isLoading: false,
    items: [],
    errors: [],
    dialogEdit: false,
    dialogDelete: false,
    editIndex: -1,
    editItem: {},
    categoryItems: constants.categoryItems,
    categoryDetailItems: constants.categoryDetailItems,
    mdText: "",
    htmlText: "",
    headers: [
      // {
      //   text: "ステータス",
      //   value: "completeView",
      //   align: "center",
      //   width: "120px",
      // },
      // {
      //   text: "カテゴリ",
      //   value: "categoryView",
      //   align: "left",
      //   width: "200px",
      // },
      {
        text: "タイトル",
        value: "title",
        align: "left",
        width: "100px",
      },
      // {
      //   text: "更新日時",
      //   value: "updatedAt",
      //   align: "center",
      //   width: "250px",
      // },
      // {
      //   text: " ",
      //   value: "actions",
      //   align: "center",
      //   width: "100px",
      // },
    ],
    toolbars: {
      underline: false,
      strikethrough: true,
      mark: true,
      superscript: false,
      subscript: false,
      quote: true,
      link: true,
      imagelink: true,
      code: true,
      table: false,
      header: false,
      bold: false,
      italic: false,
      ol: false,
      ul: false,
      fullscreen: false,
      readmodel: false,
      htmlcode: false,
      help: false,
      undo: false,
      redo: false,
      trash: false,
      save: false,
      navigation: true,
      alignleft: true,
      aligncenter: true,
      alignright: true,
      subfield: false,
      preview: true,
    },
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
      const limit = "limit=100";
      const response = await axios
        .get(
          "https://takamori-c.microcms.io/api/v1/favorites?" +
            filter +
            "&" +
            limit,
          {
            headers: {
              "X-MICROCMS-API-KEY": process.env.VUE_APP_X_MICROCMS_API_KEY,
            },
          }
        )
        .then((res) => {
          res.data.contents.forEach((data) => {
            data.category = data.category[0];
            data.categoryDetail = data.categoryDetail[0];
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
      const params = {
        title: this.editItem.title,
        content: this.editItem.content,
        contentHtml: this.editItem.contentHtml,
        category: [this.editItem.category],
        categoryDetail: [this.editItem.categoryDetail],
      };
      await axios
        .post("https://takamori-c.microcms.io/api/v1/favorites", params, {
          headers: {
            "X-MICROCMS-API-KEY": process.env.VUE_APP_X_MICROCMS_API_KEY,
          },
        })
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
        contentHtml: this.editItem.contentHtml,
        category: [this.editItem.category],
        categoryDetail: [this.editItem.categoryDetail],
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
      };
      this.dialogEdit = true;
    },
    updateItem(item) {
      // eslint-disable-next-line no-debugger
      //debugger;v-
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
    deleteDialogEdit() {
      this.editItem = Object.assign(this.items[this.editIndex], this.editItem);
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
      this.closeDialogEdit();
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
    changeEditor(value, render) {
      this.mdText = value;
      this.htmlText = render;
    },
  },
};
</script>

<style lang="scss" scoped>
:deep(.v-data-table__mobile-row__cell) {
  width: 100% !important;
}
</style>
