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
