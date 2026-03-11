<script setup lang="ts">
import { ref, computed } from "vue";
import SidebarMenu from "@/components/SidebarMenu.vue";
import SmartTable from "@/components/SmartTable.vue";
import type { ColumnConfig } from "@/components/SmartTable.vue";
import TeamView from "@/views/TeamView.vue";
import logTableData from "@/data/logTableData.json";

const activeItem = ref("Table");

const basicColumns: ColumnConfig[] = [
  { type: "number", header: "ID", attribute: "id" },
  { type: "string", header: "RequestID", attribute: "requestId" },
  { type: "badge", header: "Level", attribute: "levelDisplay" },
  { type: "string", header: "Created", attribute: "created" },
  { type: "number", header: "Status", attribute: "statusCode" },
];

const fullColumns: ColumnConfig[] = [
  { type: "number", header: "ID", attribute: "id" },
  { type: "string", header: "RequestID", attribute: "requestId" },
  { type: "number", header: "Level", attribute: "level" },
  { type: "badge", header: "LevelDisplay", attribute: "levelDisplay" },
  { type: "string", header: "Created", attribute: "created" },
  { type: "number", header: "StatusCode", attribute: "statusCode" },
  { type: "string", header: "Method", attribute: "method" },
  { type: "string", header: "Endpoint", attribute: "endpoint" },
  { type: "number", header: "Duration (ms)", attribute: "duration" },
  { type: "string", header: "UserID", attribute: "userId" },
  { type: "string", header: "IP Address", attribute: "ipAddress" },
  { type: "string", header: "User Agent", attribute: "userAgent" },
  { type: "string", header: "Environment", attribute: "environment" },
  { type: "string", header: "Service", attribute: "service" },
  { type: "string", header: "TraceID", attribute: "traceId" },
  { type: "string", header: "SpanID", attribute: "spanId" },
  { type: "string", header: "Region", attribute: "region" },
  { type: "string", header: "Version", attribute: "version" },
  { type: "string", header: "ErrorCode", attribute: "errorCode" },
  { type: "string", header: "Message", attribute: "message" },
];

const tableData = logTableData as unknown as Record<string, unknown>[];

const currentColumns = computed(() => (activeItem.value === "Table h-scroll" ? fullColumns : basicColumns));
const isScrollable = computed(() => activeItem.value === "Table h-scroll");
</script>

<template>
  <div class="layout">
    <SidebarMenu @select="activeItem = $event" />
    <main class="content">
      <TeamView v-if="activeItem === 'Team'" />
      <SmartTable v-else :key="activeItem" :title="activeItem" :columns="currentColumns" :data="tableData" />
    </main>
  </div>
</template>

<style scoped>
.layout {
  display: flex;
  height: 100vh;
  overflow: hidden;
  background: #f0f0f5;
}

.content {
  flex: 1;
  min-width: 0;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
}
</style>
