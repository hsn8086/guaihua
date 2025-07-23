<template>
  <v-card class="mx-auto my-8 elevation-10 rounded-xl" max-width="420">
    <v-container class="pa-4">
      <div
        class="text-h4 font-weight-bold mb-4 text-center text-uppercase letter-spacing-wide"
      >
        你想看谁的怪话呢?
      </div>
      <v-row>
        <v-col
          v-for="(item, i) in items"
          :key="i"
          cols="12"
          md="6"
          class="d-flex justify-center"
        >
          <v-tooltip location="top">
            <template #activator="{ props }">
              <div
                class="selectable-img"
                @click="goToSearch(item.id)"
                v-bind="props"
              >
                <v-img
                  :src="item.src"
                  height="140"
                  width="140"
                  class="rounded-circle elevation-4"
                  cover
                ></v-img>
                <div class="text-center mt-2 font-weight-medium">
                  {{ item.name }}
                </div>
              </div>
            </template>
            <span>{{ item.description }}</span>
          </v-tooltip>
        </v-col>
      </v-row>
    </v-container>
  </v-card>
</template>

<script setup>
import { useRouter } from "vue-router";
const router = useRouter();

const infos = import.meta.glob("@/assets/info/*.json", {
  eager: true,
  import: "default",
});
const items = Object.values(infos).map((info) => ({
  src: `/avatar/${info.img}`,
  ...info,
}));

function goToSearch(id) {
  router.push(`/search?id=${id}`);
}
</script>

<style scoped>
.letter-spacing-wide {
  letter-spacing: 0.2em;
}
.selectable-img {
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
  padding: 8px;
  border-radius: 16px;
}
.selectable-img:hover {
  transform: scale(1.04);
}
.selected-img {
  border: 2.5px solid #1976d2;
  box-shadow: 0 2px 16px rgba(25, 118, 210, 0.18);
  background: #e3f2fd;
}
.rounded-circle {
  border-radius: 50%;
  object-fit: cover;
}
</style>
