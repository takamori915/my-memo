<template>
  <header>
    <v-app-bar app dark clipped-left fixed dense>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-avatar size="36px" class="mx-2"></v-avatar>
      <v-toolbar-tile>
        <router-link :to="{ name: 'home' }" class="v-toolbar__title-link">
          Takamori-memo
        </router-link>
      </v-toolbar-tile>
      <v-spacer></v-spacer>
      <v-btn icon class="ma-2" to="/search">
        <v-icon>mdi-magnify</v-icon>
      </v-btn>
    </v-app-bar>
  </header>
</template>

<script>
export default {
  data: () => {
    return {
      drawer: false,
    };
  },
};
</script>
