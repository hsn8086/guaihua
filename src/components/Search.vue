<template>
  <div class="search-page">
    <v-text-field v-model="searchText" label="搜索文本/标签" clearable />
    <v-chip-group v-if="allTags.length" multiple>
      <v-chip
        v-for="tag in allTags"
        :key="tag"
        :color="selectedTags.includes(tag) ? 'primary' : ''"
        @click="toggleTag(tag)"
        class="ma-1"
      >
        {{ tag }}
      </v-chip>
    </v-chip-group>
    <v-row>
      <v-col
        v-for="item in filteredItems"
        :key="item.img"
        cols="12"
        sm="6"
        md="4"
        lg="3"
      >
        <v-card @click="showDialog(item)" class="ma-2" hover>
          <v-img :src="getImgUrl(item.img)" height="200px" />
          <v-card-title>{{ item.text }}</v-card-title>
          <v-card-subtitle>
            {{ item.tags?.length ? item.tags.join(", ") : "无" }}
          </v-card-subtitle>
        </v-card>
      </v-col>
    </v-row>
    <v-dialog v-model="dialog" max-width="600px">
      <v-card v-if="selectedItem">
        <v-img :src="getImgUrl(selectedItem.img)" height="400px" />
        <v-card-title>{{ selectedItem.text }}</v-card-title>
        <v-card-subtitle>
          {{ selectedItem.tags?.length ? selectedItem.tags.join(", ") : "无" }}
        </v-card-subtitle>
        <v-card-text>
          <div>来源: {{ selectedItem.source }}</div>
          <div>时间: {{ selectedItem.time }}</div>
        </v-card-text>
        <v-card-actions>
          <v-btn :href="getImgUrl(selectedItem.img)" download color="primary"
            >下载图片</v-btn
          >
          <v-spacer />
          <v-btn text @click="dialog = false">关闭</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from "vue";
import { useRoute } from "vue-router";

const route = useRoute();
const searchText = ref("");
const dialog = ref(false);
const selectedItem = ref<any>(null);
const items = ref<any[]>([]);
const allTags = ref<string[]>([]);
const selectedTags = ref<string[]>([]);

function getImgUrl(img: string) {
  const id = route.query.id;
  return `/record/${id}/${img}`;
}

function showDialog(item: any) {
  selectedItem.value = item;
  dialog.value = true;
}

function toggleTag(tag: string) {
  if (selectedTags.value.includes(tag)) {
    selectedTags.value = selectedTags.value.filter((t) => t !== tag);
  } else {
    selectedTags.value.push(tag);
  }
}

const filteredItems = computed(() => {
  let result = items.value;
  if (searchText.value) {
    result = result.filter(
      (item) =>
        item.text.includes(searchText.value) ||
        (item.tags &&
          item.tags.some((t: string) => t.includes(searchText.value))) ||
        (item.aliases &&
          item.aliases.some((a: string) => a.includes(searchText.value)))
    );
  }
  if (selectedTags.value.length) {
    result = result.filter(
      (item) =>
        item.tags && selectedTags.value.every((tag) => item.tags.includes(tag))
    );
  }
  return result;
});

onMounted(async () => {
  // 导入所有json
  const ctx = import.meta.glob("/src/assets/record/*/*.json", { eager: true });
  const arr: any[] = [];
  Object.values(ctx).forEach((mod: any) => {
    if (mod.default) arr.push(mod.default);
    else arr.push(mod);
  });
  // 用id过滤
  const id = route.query.id;
  if (!id) return;
  const filtered = arr.filter((item) => item.id === id);
  items.value = filtered;
  // 收集标签
  const tagSet = new Set<string>();
  filtered.forEach((item) => {
    if (item.tags) item.tags.forEach((t: string) => tagSet.add(t));
  });
  allTags.value = Array.from(tagSet);
});
</script>

<style scoped>
.search-page {
  padding: 24px;
}
</style>
