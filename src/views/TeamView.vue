<script setup lang="ts">
import SmartTable from "@/components/SmartTable.vue";
import type { ColumnConfig } from "@/components/SmartTable.vue";
import teamData from "@/data/teamData.json";

const columns: ColumnConfig[] = [
  { type: "string", header: "Name", attribute: "name" },
  { type: "string", header: "Role", attribute: "role" },
  { type: "string", header: "Department", attribute: "department" },
  { type: "string", header: "Status", attribute: "status" },
  { type: "number", header: "Tasks done", attribute: "tasksDone" },
  { type: "number", header: "Progress", attribute: "progress" },
  { type: "string", header: "Joined", attribute: "joined" },
  { type: "string", header: "Actions", attribute: "actions" },
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
    <!-- Avatar + name combined -->
    <template #cell-name="{ value, row }">
      <div class="cell-user">
        <div class="avatar" :style="{ background: (row as any).color }">
          {{ (row as any).avatar }}
        </div>
        <span>{{ value }}</span>
      </div>
    </template>

    <!-- Status dot -->
    <template #cell-status="{ value }">
      <div class="cell-status">
        <span class="status-dot" :class="'dot-' + value" />
        <span class="status-label">{{ value }}</span>
      </div>
    </template>

    <!-- Progress bar -->
    <template #cell-progress="{ value }">
      <div class="cell-progress">
        <div class="progress-bar">
          <div class="progress-fill" :style="{ width: value + '%' }" :class="progressClass(Number(value))" />
        </div>
        <span class="progress-pct">{{ value }}%</span>
      </div>
    </template>

    <!-- Action buttons -->
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

<style scoped>
/* Avatar + name */
.cell-user {
  display: flex;
  align-items: center;
  gap: 10px;
}

.avatar {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 11px;
  font-weight: 700;
  color: #fff;
  flex-shrink: 0;
  letter-spacing: 0.3px;
}

/* Status */
.cell-status {
  display: flex;
  align-items: center;
  gap: 6px;
}

.status-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  flex-shrink: 0;
}

.dot-active {
  background: #2a9d5c;
}
.dot-away {
  background: #f4a261;
}
.dot-offline {
  background: #bbb;
}

.status-label {
  font-size: 13px;
  color: #555;
  text-transform: capitalize;
}

/* Progress */
.cell-progress {
  display: flex;
  align-items: center;
  gap: 10px;
  min-width: 130px;
}

.progress-bar {
  flex: 1;
  height: 6px;
  background: #eee;
  border-radius: 3px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  border-radius: 3px;
  transition: width 0.3s;
}

.fill-high {
  background: #2a9d5c;
}
.fill-mid {
  background: #f4a261;
}
.fill-low {
  background: #e76f51;
}

.progress-pct {
  font-size: 12px;
  color: #888;
  width: 34px;
  text-align: right;
}

/* Actions */
.cell-actions {
  display: flex;
  align-items: center;
  gap: 4px;
}

.action-btn {
  background: none;
  border: none;
  cursor: pointer;
  padding: 5px;
  border-radius: 6px;
  color: #666;
  display: flex;
  align-items: center;
  transition:
    background 0.15s,
    color 0.15s;
}

.action-btn:hover {
  background: #f0f0f0;
  color: #333;
}

.action-btn.danger:hover {
  background: #fce8e8;
  color: #e53935;
}

.action-btn .material-symbols-outlined {
  font-size: 18px;
}
</style>
