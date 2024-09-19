<script lang="ts">
import { defineComponent } from 'vue';
import CryptoJS from 'crypto-js';
type CurrencyType = 'bitcoin' | 'ethereum' | 'litecoin' | 'poorcoin';

export default defineComponent({
  data() {
    return {
      blockData: '',
      blockValue: 0,
      blockCurrency: 'bitcoin' as CurrencyType,
      blockchains: {
        bitcoin: new Blockchain('bitcoin'),
        ethereum: new Blockchain('ethereum'),
        litecoin: new Blockchain('litecoin'),
        poorcoin: new Blockchain('poorcoin'),
      } as Record<CurrencyType, Blockchain>,
    };
  },
  methods: {
    createBlock(action: 'buy' | 'sell') {
      let value = parseFloat(this.blockValue.toString());
      if (action === 'sell') value = -Math.abs(value);

      const newBlock = new Block(
        this.blockchains[this.blockCurrency].chain.length,
        new Date().toLocaleString(),
        { data: this.blockData, value: value }
      );

      this.blockchains[this.blockCurrency].addBlock(newBlock);

      // Save to local storage
      localStorage.setItem('blockchains', JSON.stringify(this.blockchains));

      this.$emit('blockchainUpdated', this.blockCurrency, this.blockchains[this.blockCurrency]);
          // Reset the form
          this.resetForm();
    },
    resetForm() {
      this.blockData = '';
      this.blockValue = 0;
      this.blockCurrency = 'bitcoin';
    },
  },
});
// Block class
class Block {
  index: number;
  timestamp: string;
  data: { data: string; value: number };
  previousHash: string;
  hash: string;
  nonce: number;

  constructor(index: number, timestamp: string, data: { data: string; value: number }, previousHash = '') {
    this.index = index;
    this.timestamp = timestamp;
    this.data = data;
    this.previousHash = previousHash;
    this.hash = this.calculateHash();
    this.nonce = 0;
  }

  calculateHash(): string {
    return CryptoJS.SHA256(
      this.index + this.timestamp + JSON.stringify(this.data) + this.previousHash + this.nonce
    ).toString();
  }

  mineBlock(difficulty: number) {
    const target = Array(difficulty + 1).join('0');
    while (this.hash.substring(0, difficulty) !== target) {
      this.nonce++;
      this.hash = this.calculateHash();
    }
  }
}

// Blockchain class
class Blockchain {
  currency: string;
  difficulty: number;
  chain: Block[];

  constructor(currency: string) {
    this.currency = currency;
    this.difficulty = 4;
    this.chain = [this.createGenesisBlock()];
  }

  createGenesisBlock(): Block {
    return new Block(0, new Date().toLocaleString(), { data: 'Genesis Block', value: 0 }, '0');
  }

  getLatestBlock(): Block {
    return this.chain[this.chain.length - 1];
  }

  addBlock(newBlock: Block) {
    newBlock.previousHash = this.getLatestBlock().hash;
    newBlock.mineBlock(this.difficulty);
    this.chain.push(newBlock);
  }
}
</script>

<template>

        <!-- Left Column: Form for Creating Blockchain -->
        <div class="lg:w-full bg-white p-4 rounded shadow-lg">
          <h4 class="text-xl font-bold mb-4">Create a Block</h4>
  <form @submit.prevent="createBlock('buy')">
    <div class="mb-3">
      <label class="block text-sm font-medium text-gray-700" for="blockData">Block Data:</label>
      <textarea class="w-full border border-gray-300 rounded-lg p-2" id="blockData" v-model="blockData" required></textarea>
    </div>
    <div class="mb-3">
      <label class="block text-sm font-medium text-gray-700" for="blockValue">Block Value (Currency):</label>
      <input class="w-full border border-gray-300 rounded-lg p-2" type="text" v-model="blockValue" required />
    </div>
    <div class="mb-3">
      <label class="block text-sm font-medium text-gray-700" for="blockCurrency">Select Cryptocurrency:</label>
      <select class="w-full border border-gray-300 rounded-lg p-2" v-model="blockCurrency" required>
        <option value="bitcoin">Bitcoin (BTC)</option>
        <option value="ethereum">Ethereum (ETH)</option>
        <option value="litecoin">Litecoin (LTC)</option>
        <option value="poorcoin">Poor.Coin (POC)</option>
      </select>
    </div>
    <div class="text-center">
      <button class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded mt-4"  type="button" @click="createBlock('buy')">Buy</button>
      <button class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded mt-4 ml-2" type="button" @click="createBlock('sell')">Sell</button>
    </div>
    
  </form>
</div>


</template>

<style>

</style>
