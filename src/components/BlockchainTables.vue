<script lang="ts">
import { defineComponent, PropType } from 'vue';

interface Block {
  index: number;
  timestamp: string;
  data: {
    data: string;
    value: number;
  };
  hash: string;
  previousHash: string;
  nonce: number;
}

interface Blockchain {
  chain: Block[];
}

export default defineComponent({
  props: {
    blockchain: {
      type: Object as PropType<Blockchain>,
      required: true,
    },
  },
  computed: {
    totalValue(): number {
      return this.blockchain.chain.reduce((total, block) => total + (parseFloat(block.data.value.toString()) || 0), 0);
    },
  },
});
</script>

<template>
  
    <div v-if="blockchain">
      <table class="table table-striped-columns myTable">
        <thead>
          <tr>
            <th>Block Number</th>
            <th>Timestamp</th>
            <th>Data</th>
            <th>Hash</th>
            <th>Previous Hash</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="block in blockchain.chain" :key="block.index">
            <td>{{ block.index }}</td>
            <td>{{ block.timestamp }}</td>
            <td>{{ block.data.data }}</td>
            <td>{{ block.hash }}</td>
            <td>{{ block.previousHash }}</td>
          </tr>
          <tr>
            <td colspan="5">Total Value: {{ totalValue }}</td>
          </tr>
        </tbody>
      </table>
    </div>

</template>

<style scoped>
/* Styles */
</style>
