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
