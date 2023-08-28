<template>
  <header>
    <v-app-bar app dark clipped-left fixed dense>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-avatar size="36px" class="mx-2">
        <v-img
          alt="takamori-memo"
          src="https://images.microcms-assets.io/assets/16a96ec0ede542a2aacfbaeb19902192/41118b68f6ea453383490826f8a199f8/logo.png"
        />
      </v-avatar>
      <v-toolbar-title>
        <router-link :to="{ name: 'Home' }" class="v-toolbar__title-link">
          Takamori-memo
        </router-link>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn icon class="ma-2" to="/search">
        <v-icon>mdi-magnify</v-icon>
      </v-btn>
      <v-btn outlined to="/login"> ログイン </v-btn>
    </v-app-bar>
    <v-navigation-drawer v-model="drawer" dark app clipped>
      <v-list nav dense>
        <v-list-item
          v-for="(menuItem, index) in menuItems"
          :key="index"
          :to="menuItem.url"
        >
          <v-list-item-content>
            <v-list-item-title>{{ menuItem.name }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
  </header>
</template>

<script>
import constants from "../common/constants";

export default {
  data: () => {
    return {
      drawer: false,
      menuItems: constants.menuItems,
    };
  },
};
</script>

<style lang="scss" scoped>
.v-toolbar__title {
  overflow: visible !important;
  margin-right: 50px !important;
  &-link {
    text-decoration: none;
    color: inherit !important;
  }
}
</style>
