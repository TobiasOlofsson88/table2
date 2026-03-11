<script setup lang="ts">
import { ref } from "vue";

const emit = defineEmits<{ (e: "select", label: string): void }>();

const filterText = ref("");

interface MenuItem {
  label: string;
  icon: string;
}

interface MenuSection {
  title: string;
  items: MenuItem[];
}

const sections: MenuSection[] = [
  {
    title: "TABLES",
    items: [
      { label: "Table", icon: "table_rows" },
      { label: "Table h-scroll", icon: "table" },
    ],
  },
  {
    title: "EXAMPLES",
    items: [{ label: "Team", icon: "group" }],
  },
];

const activeItem = ref("Table");

function setActive(label: string) {
  activeItem.value = label;
  emit("select", label);
}
</script>

<template>
  <aside class="sidebar">
    <div class="sidebar-header">
      <span class="sidebar-logo">Portal</span>
    </div>

    <div class="sidebar-filter">
      <input v-model="filterText" type="text" placeholder="Filter" class="filter-input" />
    </div>

    <nav class="sidebar-nav">
      <div v-for="section in sections" :key="section.title" class="menu-section">
        <div class="section-title">{{ section.title }}</div>
        <ul class="menu-list">
          <li v-for="item in section.items" :key="item.label" class="menu-item" :class="{ active: activeItem === item.label }" @click="setActive(item.label)">
            <span class="material-symbols-outlined menu-icon">{{ item.icon }}</span>
            <span class="menu-label">{{ item.label }}</span>
          </li>
        </ul>
      </div>
    </nav>
  </aside>
</template>
