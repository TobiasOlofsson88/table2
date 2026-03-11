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
      <span class="sidebar-logo">SDP Södertälje</span>
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

<style scoped>
.sidebar {
  width: 240px;
  min-width: 240px;
  height: 100vh;
  background: #fff;
  border-right: 1px solid #e8e8e8;
  display: flex;
  flex-direction: column;
  overflow-y: auto;
}

.sidebar-header {
  padding: 20px 16px 12px;
}

.sidebar-logo {
  font-size: 18px;
  font-weight: 600;
  color: #1a1a2e;
}

.sidebar-filter {
  padding: 0 16px 12px;
}

.filter-input {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  font-size: 14px;
  outline: none;
  color: #666;
}

.filter-input:focus {
  border-color: #2a9d5c;
}

.sidebar-nav {
  flex: 1;
  padding: 0 8px;
}

.menu-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.menu-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 10px 12px;
  border-radius: 8px;
  cursor: pointer;
  color: #555;
  font-size: 14px;
  transition: background 0.15s;
}

.menu-item:hover {
  background: #edfaf3;
}

.menu-item.active {
  background: #d9f5e8;
  color: #1e7a45;
}

.menu-icon {
  font-size: 20px;
}

.menu-label {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.menu-section {
  margin-top: 8px;
}

.section-title {
  padding: 12px 12px 4px;
  font-size: 11px;
  font-weight: 600;
  color: #999;
  letter-spacing: 0.5px;
  text-transform: uppercase;
}
</style>
