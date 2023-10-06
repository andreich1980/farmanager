<script setup>
const props = defineProps({
  columns: Array,
  data: Array,
  rowClasses: Function,
});

const emit = defineEmits(["active-row"]);

const getColumnData = (row, column) => {
  if (typeof column.data === "string") {
    return row[column.data];
  }
  if (typeof column.data === "function") {
    return column.data(row);
  }
};

const getClassesForRow = (row) => {
  if (typeof props.rowClasses === "function") {
    return props.rowClasses(row);
  }
  return [];
};
</script>

<template>
  <table>
    <thead>
      <tr class="divide-x divide-light-blue">
        <th
          v-for="column in columns"
          class="text-yellow text-center px-1 pt-3"
          :class="column.classHeader"
        >
          {{ column.title }}
        </th>
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="row in data"
        class="divide-x divide-light-blue"
        :class="getClassesForRow(row)"
        @click="emit('active-row', row)"
      >
        <td
          v-for="column in columns"
          class="text-light-blue"
          :class="column.classRow"
        >
          <slot
            :name="`td-${column.name}`"
            :row="row"
            :value="getColumnData(row, column)"
          >
            {{ getColumnData(row, column) }}
          </slot>
        </td>
      </tr>
    </tbody>
  </table>
</template>
