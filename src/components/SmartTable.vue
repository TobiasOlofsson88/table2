<script setup lang="ts">
import { ref, computed } from "vue";
import SmartTableRow from "./SmartTableRow.vue";

export interface ColumnConfig {
  type: string;
  header: string;
  attribute: string;
}

const props = withDefaults(
  defineProps<{
    columns: ColumnConfig[];
    data: Record<string, unknown>[];
    pageSize?: number;
    title?: string;
  }>(),
  {
    pageSize: 10,
    title: "Table",
  },
);

// --- Filtering ---
const searchQuery = ref("");

const filteredData = computed(() => {
  if (!searchQuery.value) return props.data;
  const q = searchQuery.value.toLowerCase();

  // search across all string/number/badge columns
  const searchable = props.columns.filter((c) => ["string", "number", "badge"].includes(c.type));

  return props.data.filter((row) =>
    searchable.some((col) => {
      const val = row[col.attribute];
      return val != null && String(val).toLowerCase().includes(q);
    }),
  );
});

// --- Pagination ---
const currentPage = ref(1);
const perPage = ref(props.pageSize);

const totalItems = computed(() => filteredData.value.length);
const totalPages = computed(() => Math.max(1, Math.ceil(totalItems.value / perPage.value)));

const paginatedData = computed(() => {
  const start = (currentPage.value - 1) * perPage.value;
  return filteredData.value.slice(start, start + perPage.value);
});

const paginationLabel = computed(() => {
  if (totalItems.value === 0) return "0 of 0";
  const start = (currentPage.value - 1) * perPage.value + 1;
  const end = Math.min(currentPage.value * perPage.value, totalItems.value);
  return `${start}–${end} of ${totalItems.value}`;
});

function prevPage() {
  if (currentPage.value > 1) currentPage.value--;
}
function nextPage() {
  if (currentPage.value < totalPages.value) currentPage.value++;
}

// Reset to page 1 when search changes
import { watch } from "vue";
watch(searchQuery, () => {
  currentPage.value = 1;
});
</script>

<template>
  <div class="smart-table">
    <!-- Header bar -->
    <div class="header-bar">
      <div class="header-left">
        <button class="icon-btn"><span class="material-symbols-outlined">menu</span></button>
        <h1 class="page-title">{{ props.title }}</h1>
      </div>
      <div class="header-right">
        <button class="icon-btn"><span class="material-symbols-outlined">search</span></button>
        <button class="icon-btn"><span class="material-symbols-outlined">calendar_today</span></button>
        <button class="icon-btn notification-btn">
          <span class="material-symbols-outlined">notifications</span>
          <span class="notification-badge">1</span>
        </button>
        <button class="icon-btn"><span class="material-symbols-outlined">settings</span></button>
      </div>
    </div>

    <!-- Search toolbar -->
    <div class="toolbar">
      <div class="search-wrapper">
        <span class="material-symbols-outlined search-icon">search</span>
        <input v-model="searchQuery" type="text" placeholder="Search..." class="search-input" />
      </div>
      <div class="toolbar-actions">
        <button class="icon-btn" title="Refresh"><span class="material-symbols-outlined">refresh</span></button>
        <button class="icon-btn" title="Download"><span class="material-symbols-outlined">download</span></button>
        <button class="icon-btn" title="Filter"><span class="material-symbols-outlined">filter_alt</span></button>
        <button class="search-btn">Search</button>
      </div>
    </div>

    <!-- Table card -->
    <div class="table-card">
      <div class="table-scroll">
        <table>
          <thead>
            <tr>
              <th v-for="col in columns" :key="col.attribute">
                {{ col.header }}
              </th>
            </tr>
          </thead>
          <tbody>
            <SmartTableRow v-for="(row, i) in paginatedData" :key="i" :columns="columns" :row="row">
              <!-- Forward all cell-* slots from the parent through to the row -->
              <template v-for="(_, name) in $slots" #[name]="slotProps">
                <slot :name="name" v-bind="slotProps" />
              </template>
            </SmartTableRow>
            <tr v-if="paginatedData.length === 0">
              <td :colspan="columns.length" class="empty-row">No data found</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Pagination -->
    <div class="pagination">
      <span class="pagination-label">{{ paginationLabel }}</span>
      <div class="pagination-controls">
        <button class="page-btn" :disabled="currentPage <= 1" @click="prevPage">
          <span class="material-symbols-outlined">chevron_left</span>
        </button>
        <select v-model="perPage" class="per-page-select">
          <option :value="10">10</option>
          <option :value="25">25</option>
          <option :value="50">50</option>
        </select>
        <span class="per-page-text">per page</span>
        <button class="page-btn" :disabled="currentPage >= totalPages" @click="nextPage">
          <span class="material-symbols-outlined">chevron_right</span>
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.smart-table {
  display: flex;
  flex-direction: column;
  gap: 0;
}

/* Header bar */
.header-bar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px 24px;
  background: #fff;
  border-bottom: 1px solid #eee;
  margin-bottom: 0;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 12px;
}

.page-title {
  font-size: 18px;
  font-weight: 500;
  color: #333;
}

.header-right {
  display: flex;
  align-items: center;
  gap: 4px;
}

.notification-btn {
  position: relative;
}

.notification-badge {
  position: absolute;
  top: 4px;
  right: 4px;
  background: #2a9d5c;
  color: #fff;
  font-size: 10px;
  font-weight: 600;
  width: 16px;
  height: 16px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* Toolbar */
.toolbar {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 16px 24px;
  background: #f0f0f5;
}

.search-wrapper {
  flex: 1;
  display: flex;
  align-items: center;
  background: #fff;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  padding: 0 12px;
}

.search-icon {
  color: #bbb;
  font-size: 20px;
}

.search-input {
  flex: 1;
  border: none;
  outline: none;
  padding: 10px 8px;
  font-size: 14px;
  color: #333;
  background: transparent;
}

.toolbar-actions {
  display: flex;
  align-items: center;
  gap: 4px;
}

.icon-btn {
  background: none;
  border: none;
  cursor: pointer;
  padding: 8px;
  border-radius: 8px;
  color: #666;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background 0.15s;
}

.icon-btn:hover {
  background: #ebebeb;
}

.search-btn {
  background: #2a9d5c;
  color: #fff;
  border: none;
  padding: 10px 24px;
  border-radius: 8px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: background 0.15s;
}

.search-btn:hover {
  background: #1e7a45;
}

.toolbar-info {
  font-size: 13px;
  color: #888;
  white-space: nowrap;
}

/* Table card */
.table-card {
  background: #fff;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.06);
  border: 1px solid #ececec;
  margin: 0 24px;
}

.table-scroll {
  overflow-x: auto;
  scrollbar-width: thin;
  scrollbar-color: #d0d0d0 transparent;
}

.table-scroll::-webkit-scrollbar {
  height: 6px;
}
.table-scroll::-webkit-scrollbar-thumb {
  background: #d0d0d0;
  border-radius: 3px;
}

table {
  width: 100%;
  min-width: max-content;
  border-collapse: collapse;
  table-layout: auto;
}

thead {
  background: #fafafe;
}

th {
  text-align: left;
  padding: 12px 14px;
  font-size: 12px;
  font-weight: 600;
  color: #888;
  border-bottom: 1px solid #eee;
  white-space: nowrap;
}

.empty-row {
  text-align: center;
  padding: 32px 14px;
  color: #aaa;
  font-size: 14px;
}

/* Pagination */
.pagination {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: 16px;
  padding: 16px 24px;
}

.pagination-label {
  font-size: 13px;
  color: #888;
}

.pagination-controls {
  display: flex;
  align-items: center;
  gap: 8px;
}

.page-btn {
  background: none;
  border: none;
  cursor: pointer;
  padding: 6px;
  border-radius: 6px;
  color: #666;
  display: flex;
  align-items: center;
}

.page-btn:hover {
  background: #f0f0f0;
}

.page-btn:disabled {
  opacity: 0.3;
  cursor: default;
}

.per-page-select {
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  padding: 4px 8px;
  font-size: 13px;
  color: #555;
  outline: none;
}

.per-page-text {
  font-size: 13px;
  color: #888;
}
</style>
