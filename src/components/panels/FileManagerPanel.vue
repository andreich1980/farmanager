<script setup>
import { computed, onMounted, ref } from "vue";
import Card from "../Card.vue";
import DataTable from "../data-table/DataTable.vue";

const ITEM_TYPES = {
  UP: "up",
  FOLDER: "folder",
  FILE: "file",
};

const PARENT_FOLDER = {
  path: "..",
  type: ITEM_TYPES.UP,
};

defineProps({
  active: Boolean,
});

const drive = ref("c");

const structure = ref([]);
const currentPath = ref([]);

const activeRow = ref(null);
const setActiveRow = (row) => {
  if (activeRow.value !== row.path) {
    activeRow.value = row.path;
  } else {
    handleDoubleClick(row);
  }
};

onMounted(async () => {
  const response = await fetch(`files/${drive.value}.json`);
  structure.value = await response.json();
});

const currentLevelStructure = computed(() => {
  let levelData = structure.value;
  for (const path of currentPath.value) {
    levelData = [
      PARENT_FOLDER,
      ...levelData.find((item) => item.path === path).children,
    ];
  }
  return levelData
    .map((item) => {
      let type = item.type;
      type ??= item.children ? ITEM_TYPES.FOLDER : ITEM_TYPES.FILE;
      return {
        ...item,
        type,
      };
    })
    .sort((a, b) => {
      if (a.type === ITEM_TYPES.UP) {
        return -1;
      }
      if (b.type === ITEM_TYPES.UP) {
        return 1;
      }

      if (a.type !== b.type) {
        return a.type === ITEM_TYPES.FOLDER ? -1 : 1;
      }

      return a.path < b.path ? -1 : 1;
    });
});

const handleDoubleClick = (row) => {
  if (row.type === "up") {
    activeRow.value = null;
    currentPath.value.pop();
    return;
  }
  if (row.type === ITEM_TYPES.FOLDER) {
    activeRow.value = null;
    currentPath.value.push(row.path);
    return;
  }
  // It's a FILE
};

const path = computed(
  () => `${drive.value.toUpperCase()}:\\${currentPath.value.join("\\")}`,
);

const columns = [
  {
    title: "Name",
    name: "path",
    data: "path",
    classHeader: "w-6/12",
  },
  {
    title: "Size",
    name: "size",
    data: ({ size }) => prepareFileSize(size),
    classHeader: "w-2/12",
  },
  {
    title: "Date",
    name: "date",
    data: ({ created_at }) => created_at?.split(" ")[0] ?? "",
    classHeader: "w-2/12",
    classRow: "text-center",
  },
  {
    title: "Time",
    name: "time",
    data: ({ created_at }) => created_at?.split(" ")[1] ?? "",
    classHeader: "w-2/12",
    classRow: "text-center",
  },
];

const getTableCellClasses = ({ type, path }) => {
  const classes = ["px-1", "line-clamp-1"];
  if (type === ITEM_TYPES.FOLDER) {
    classes.push("text-white");
  }
  if (path === activeRow.value) {
    classes.push("bg-green");
  }
  return classes;
};

const getClassesForRow = (row) => {
  return ["cursor-pointer"];
};

const prepareFileSize = (size) => {
  const formatSize = (value) =>
    value.toLocaleString(undefined, { minimumFractionDigits: 2 });

  if (size > 1024 * 1024 * 1024) {
    return { value: formatSize(size / (1024 * 1024 * 1024)), unit: "G" };
  }
  if (size > 1024 * 1024) {
    return { value: formatSize(size / (1024 * 1024)), unit: "M" };
  }
  if (size > 1024) {
    return { value: formatSize(size / 1024), unit: "K" };
  }
  return { value: size };
};
</script>

<template>
  <Card :title="path" :active="active">
    <DataTable
      :columns="columns"
      :data="currentLevelStructure"
      class="table-fixed select-none"
      :row-classes="getClassesForRow"
      @active-row="setActiveRow"
    >
      <template #td-path="{ row, value: path }">
        <div :class="getTableCellClasses(row)">
          {{ path }}
        </div>
      </template>
      <template #td-size="{ row, value: size }">
        <div
          v-if="row.type === ITEM_TYPES.FILE"
          class="text-right"
          :class="getTableCellClasses(row)"
        >
          {{ size.value }} {{ size.unit }}
        </div>
        <div
          v-else
          class="flex justify-between before:content-['<'] after:content-['>']"
          :class="getTableCellClasses(row)"
        >
          <template v-if="row.type === ITEM_TYPES.UP">Up</template>
          <template v-if="row.type === ITEM_TYPES.FOLDER">Folder</template>
        </div>
      </template>
      <template #td-date="{ row, value: date }">
        <div :class="getTableCellClasses(row)" v-html="date || '&nbsp;'"></div>
      </template>
      <template #td-time="{ row, value: time }">
        <div :class="getTableCellClasses(row)" v-html="time || '&nbsp;'"></div>
      </template>
    </DataTable>
  </Card>
</template>
