<script setup lang="ts">
import SmartTable from "@/components/SmartTable.vue";
import type { ColumnConfig } from "@/components/SmartTable.vue";
import teamData from "@/data/teamData.json";

const columns: ColumnConfig[] = [
  { type: "avatar", header: "Name", attribute: "name" },
  { type: "string", header: "Role", attribute: "role" },
  { type: "string", header: "Department", attribute: "department" },
  { type: "number", header: "Tasks done", attribute: "tasksDone" },
  { type: "progress", header: "Progress", attribute: "progress" },
  { type: "string", header: "Joined", attribute: "joined" },
  { type: "actions", header: "Actions", attribute: "actions" },
];

const data = teamData as unknown as Record<string, unknown>[];

function progressClass(v: number) {
  if (v >= 75) return "fill-high";
  if (v >= 40) return "fill-mid";
  return "fill-low";
}
</script>

<template>
  <SmartTable title="Team" :columns="columns" :data="data">
    <!-- Avatar + name (matches type: "avatar") -->
    <template #cell-avatar="{ value, row }">
      <div class="cell-user">
        <div class="avatar" :style="{ background: (row as any).color }">
          {{ (row as any).avatar }}
        </div>
        <span>{{ value }}</span>
      </div>
    </template>

    <!-- Progress bar (matches type: "progress") -->
    <template #cell-progress="{ value }">
      <div class="cell-progress">
        <div class="progress-bar">
          <div class="progress-fill" :style="{ width: value + '%' }" :class="progressClass(Number(value))" />
        </div>
        <span class="progress-pct">{{ value }}%</span>
      </div>
    </template>

    <!-- Action buttons (matches type: "actions") -->
    <template #cell-actions="{ row }">
      <div class="cell-actions">
        <button class="action-btn" title="View">
          <span class="material-symbols-outlined">open_in_new</span>
        </button>
        <button class="action-btn" title="Edit">
          <span class="material-symbols-outlined">edit</span>
        </button>
        <button class="action-btn danger" title="Remove">
          <span class="material-symbols-outlined">delete</span>
        </button>
      </div>
    </template>
  </SmartTable>
</template>
