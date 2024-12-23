<template>
  <div id="app">
    <h1>CSV File Table Viewer</h1>
    <input type="file" @change="handleFileInput" accept=".csv" />
    <table v-if="tableData.length > 0" border="1">
      <thead>
        <tr>
          <th v-for="(header, index) in headers" :key="index" @click="sortTable(index)">
            {{ header }}
            <span v-if="sortConfig.key === index">
              {{ sortConfig.order === 'asc' ? '⬆️' : '⬇️' }}
            </span>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, rowIndex) in sortedData" :key="rowIndex">
          <td v-for="(cell, cellIndex) in row" :key="cellIndex">{{ cell }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      headers: [],
      tableData: [],
      sortConfig: {
        key: null,
        order: 'asc',
      },
    };
  },
  computed: {
    sortedData() {
      if (this.sortConfig.key === null) return this.tableData;

      const sorted = [...this.tableData].sort((a, b) => {
        if (a[this.sortConfig.key] < b[this.sortConfig.key]) {
          return this.sortConfig.order === 'asc' ? -1 : 1;
        }
        if (a[this.sortConfig.key] > b[this.sortConfig.key]) {
          return this.sortConfig.order === 'asc' ? 1 : -1;
        }
        return 0;
      });

      return sorted;
    },
  },
  methods: {
    handleFileInput(event) {
      const file = event.target.files[0];
      if (!file) return;

      const reader = new FileReader();
      reader.onload = (e) => {
        const text = e.target.result;
        this.parseCSV(text);
      };

      reader.readAsText(file);
    },
    parseCSV(text) {
      const rows = text.split('\n').filter((row) => row.trim() !== '');
      this.headers = rows[0].split(',');
      this.tableData = rows.slice(1).map((row) => row.split(','));
    },
    sortTable(index) {
      if (this.sortConfig.key === index) {
        this.sortConfig.order = this.sortConfig.order === 'asc' ? 'desc' : 'asc';
      } else {
        this.sortConfig.key = index;
        this.sortConfig.order = 'asc';
      }
    },
  },
};
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  margin: 20px;
}

th {
  cursor: pointer;
  user-select: none;
}

th span {
  margin-left: 5px;
}
</style>
