<script setup lang="ts">
import { ref, onMounted } from 'vue';
import BlockchainForm from './components/BlockchainForm.vue';
import BlockchainTables from './components/BlockchainTables.vue';

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

interface Blockchains {
  bitcoin: Blockchain | null;
  ethereum: Blockchain | null;
  litecoin: Blockchain | null;
  poorcoin: Blockchain | null;
}

const blockchains = ref<Blockchains>({
  bitcoin: null,
  ethereum: null,
  litecoin: null,
  poorcoin: null,
});

const loadBlockchains = () => {
  const storedBlockchains = localStorage.getItem('blockchains');
  if (storedBlockchains) {
    blockchains.value = JSON.parse(storedBlockchains);
  }
};

const saveBlockchains = () => {
  localStorage.setItem('blockchains', JSON.stringify(blockchains.value));
};

onMounted(() => {
  loadBlockchains();
});

const handleBlockchainUpdate = (currency: keyof Blockchains, updatedBlockchain: Blockchain) => {
  blockchains.value[currency] = updatedBlockchain;
  saveBlockchains();
};

</script>

<template>
  <!-- <div class="flex flex-row gap-4">
    <div class="bg-orange-500 w-1/3 *:p-12">Lorem ipsum dolor sit amet consectetur adipisicing elit.</div>
    <div class="bg-red-500 w-1/3 p-12">Lorem ipsum dolor sit.</div>
    <div class="bg-purple-500 w-1/3 p-12">Lorem ipsum dolor sit amet consectetur.</div>
  </div> -->

  <div class="lg:w-2/3 w-full mt-6 lg:mt-0 p-4 rounded shadow-lg ">
    <h1>Blockchain App</h1>
    
      <BlockchainForm @blockchainUpdated="handleBlockchainUpdate" />
    
    
    <h3 class="text-xl font-bold text-center mb-4">Bitcoin Blockchain</h3>
    <div class="overflow-x-auto">
      <BlockchainTables  :blockchain="blockchains.bitcoin" v-if="blockchains.bitcoin" />
    </div>
    
    <h3 class="text-xl font-bold text-center mb-4">Etherum Blockchain</h3>
    <div class="overflow-x-auto">
    <BlockchainTables :blockchain="blockchains.ethereum" v-if="blockchains.ethereum" />
    </div>
    <h3 class="text-xl font-bold text-center mb-4">Litecoin Blockchain</h3>
    <div class="overflow-x-auto">
    <BlockchainTables :blockchain="blockchains.litecoin" v-if="blockchains.litecoin" />
    </div>
    <h3 class="text-xl font-bold text-center mb-4">poor.coin Blockchain</h3>
    <div class="overflow-x-auto">
    <BlockchainTables :blockchain="blockchains.poorcoin" v-if="blockchains.poorcoin" />
    </div>
  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>