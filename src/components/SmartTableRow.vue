<script setup lang="ts">
import { useSlots } from "vue";
import type { ColumnConfig } from "./SmartTable.vue";

defineProps<{
  columns: ColumnConfig[];
  row: Record<string, unknown>;
}>();

const slots = useSlots();
function hasSlot(type: string) {
  return !!slots[`cell-${type}`];
}
</script>

<template>
  <tr>
    <td v-for="col in columns" :key="col.attribute">
      <!-- Custom slot takes priority (matched by column type) -->
      <template v-if="hasSlot(col.type)">
        <slot :name="'cell-' + col.type" :value="row[col.attribute]" :row="row" />
      </template>

      <!-- String -->
      <template v-else-if="col.type === 'string'">
        {{ row[col.attribute] ?? "—" }}
      </template>

      <!-- Number -->
      <template v-else-if="col.type === 'number'">
        {{ row[col.attribute] ?? "—" }}
      </template>

      <!-- Checkbox -->
      <template v-else-if="col.type === 'checkbox'">
        <input type="checkbox" :checked="!!row[col.attribute]" disabled />
      </template>

      <!-- Link -->
      <template v-else-if="col.type === 'link'">
        <a v-if="row[col.attribute]" :href="String(row[col.attribute])" target="_blank" rel="noopener noreferrer" class="cell-link">
          {{ String(row[col.attribute]) }}
        </a>
        <span v-else>—</span>
      </template>

      <!-- Badge -->
      <template v-else-if="col.type === 'badge'">
        <span v-if="row[col.attribute] != null" class="cell-badge" :class="'badge-' + String(row[col.attribute]).toLowerCase()">
          {{ row[col.attribute] }}
        </span>
        <span v-else>—</span>
      </template>

      <!-- Fallback -->
      <template v-else>
        {{ row[col.attribute] ?? "—" }}
      </template>
    </td>
  </tr>
</template>

<style scoped>
td {
  padding: 12px 14px;
  font-size: 13px;
  color: #333;
  border-bottom: 1px solid #f2f2f2;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.cell-link {
  color: #2a9d5c;
  text-decoration: none;
  font-size: 13px;
}

.cell-link:hover {
  text-decoration: underline;
}

input[type="checkbox"] {
  width: 16px;
  height: 16px;
  accent-color: #2a9d5c;
  cursor: default;
}

.cell-badge {
  display: inline-block;
  padding: 3px 12px;
  border-radius: 6px;
  font-size: 11px;
  font-weight: 600;
  text-transform: uppercase;
}

.badge-info {
  background: #e8f4fd;
  color: #2196f3;
}
.badge-warning {
  background: #fff8e1;
  color: #f9a825;
}
.badge-error {
  background: #fce8e8;
  color: #e53935;
}
.badge-success {
  background: #e6f4ea;
  color: #2a7a40;
}
</style>
