<template>
  <v-app>
    <v-app-bar app dark>
      <v-toolbar-title style="cursor: pointer" @click="$router.push('/')">
        {{ $vuetify.display.mobile ? "怪话图册" : "看看大家都说了什么怪话!" }}
      </v-toolbar-title>
      <v-spacer />
      <v-btn icon @click="toggleTheme">
        <v-icon>{{
          isDark ? "mdi-white-balance-sunny" : "mdi-moon-waning-crescent"
        }}</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <router-view />
    </v-main>

  </v-app>
</template>

<script lang="ts" setup>
import { computed } from "vue";
import { useTheme } from "vuetify";
import { useRouter } from "vue-router";

const theme = useTheme();
const isDark = computed(() => theme.global.current.value.dark);
const toggleTheme = () => {
  theme.global.name.value = isDark.value ? "light" : "dark";
};
const $router = useRouter();
</script>
