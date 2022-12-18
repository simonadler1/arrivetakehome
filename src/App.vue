<template>
  <v-app>
    <v-main>
      <v-simple-table>
        <template v-slot:default>
          <thead>
            <tr>
              <th class="text-left">Name</th>
              <th class="text-left">number of words sent</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in sortedTableData" :key="index">
              <td>{{ item[0] }}</td>
              <td>{{ item[1] }}</td>
            </tr>
          </tbody>
        </template>
      </v-simple-table>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      connection: null,
      tableData: {},
      tableKey: 0,
    };
  },
  computed: {
    sortedTableData: function () {
      this.tableKey;
      return Object.entries(this.tableData).sort(([key1], [key2]) =>
        key1.localeCompare(key2)
      );
    },
  },
  created() {
    this.connection = new WebSocket(
      "wss://tso-take-home-chat-room.herokuapp.com"
    );
    const vm = this;
    this.connection.onmessage = function (event) {
      const sender = event.data.substring(0, event.data.indexOf(":"));
      const msg = event.data.substring(event.data.indexOf(":") + 2);
      const numberOfWordsInMsg = msg.split(" ").length;
      if (!vm.tableData[sender]) {
        vm.tableData[sender] = 0;
      }
      vm.tableData[sender] += numberOfWordsInMsg;
      vm.tableKey++;
    };
  },
};
</script>
