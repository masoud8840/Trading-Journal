<template>
  <header class="header">
    <h1 class="headline">Orders</h1>
    <h3 class="subheadline">{{ totalOrders }} orders found</h3>
  </header>

  <nav class="navigation">
    <ul>
      <li v-for="cat in categories" :key="cat">
        <a
          :href="'#' + cat.link"
          :class="{ active: currentCat === cat.link }"
          @click="setCurrentCat(cat.link)"
          >{{ cat.title }}</a
        >
      </li>
    </ul>
    <plus class="plus-icon" @click="openNewCatGroupModal" />
  </nav>

  <main>
    <table-header></table-header>
    <trade-item
      v-for="(trade, index) in trades"
      :key="index"
      :id="trade.id"
      :contract="trade.contract"
      :contract-icon="trade.contractIcon"
      :contract-short="trade.contractShort"
      :value="trade.value"
      :leverage="trade.leverage"
      :amount="trade.amount"
      :side="trade.side"
      :entry="trade.entry"
      :pnl="trade.pnl"
      :liq-price="trade.liqPrice"
      :stop-loss="trade.stopLoss"
      :target-profit="trade.targetProfit"
      :open-at="trade.openAt"
      :close-on="trade.closeOn"
    ></trade-item>

    {{ trades }}
  </main>

  <section class="pagination">
    <plus class="plus-icon" @click="openNewTradeModal" />
    <div>
      <LeftChevron class="left-chevron" />
      <input type="number" name="page" id="page" value="15" />
      <span>of 192</span>
      <RightChevron class="right-chevron" />
    </div>
  </section>
  <modal-base
    modal-title="New Trade"
    v-if="newTradeModal"
    @close-modal="closeModal"
    primary-btn="add to list"
  >
    <form class="add-trade-modal-form">
      <section class="input-group">
        <label for="contract">Contract</label>
        <input
          type="text"
          id="contract"
          placeholder="BTC"
          v-model="newTrade.contract"
        />
      </section>
      <section class="input-group">
        <label for="value">value</label>
        <input
          type="number"
          id="value"
          placeholder="120"
          v-model="newTrade.value"
        />
      </section>
      <section class="input-group">
        <label for="leverage">leverage</label>
        <input
          type="number"
          id="leverage"
          placeholder="10"
          v-model="newTrade.leverage"
        />
      </section>
      <section class="input-group">
        <label for="side">Side</label>
        <select id="side" :class="newTrade.side" v-model="newTrade.side">
          <option value="long">Long</option>
          <option value="short">Short</option>
        </select>
      </section>
      <section class="input-group">
        <label for="entry">entry price</label>
        <input type="number" id="entry" placeholder="10.2" />
      </section>
      <section class="input-group">
        <label for="entry">Stop Loss / Target Profit</label>
        <div>
          <input type="number" id="entry" placeholder="9.2" />
          <span>/</span>
          <input type="number" id="entry" placeholder="15.2" />
        </div>
      </section>
    </form>
  </modal-base>
  <modal-base
    modal-title="New Category Group"
    v-if="newCatGroupModal"
    @close-modal="closeModal"
    primary-btn="add category"
  >
    <form class="add-trade-modal-form">
      <section class="input-group">
        <label for="name">Contract</label>
        <input
          type="text"
          id="name"
          placeholder="BTC"
          v-model="newTrade.contract"
        />
      </section>
      <section class="input-group">
        <label for="value">value</label>
        <input
          type="number"
          id="value"
          placeholder="120"
          v-model="newTrade.value"
        />
      </section>
    </form>
  </modal-base>
</template>

<script setup>
// Components
import TableHeader from "./components/layout/TableHeader.vue";
import TradeItem from "./components/UI/TradeItem.vue";
import ModalBase from "./components/UI/ModalBase.vue";
// icons
import plus from "./assets/Plus.vue";
import LeftChevron from "./assets/LeftChevron.vue";
import RightChevron from "./assets/RightChevron.vue";

import { ref } from "vue";

const newTradeModal = ref(false);
const newCatGroupModal = ref(false);
const closeModal = () => {
  newTradeModal.value = false;
  newCatGroupModal.value = false;
};
const openNewTradeModal = () => {
  newTradeModal.value = true;
};
const openNewCatGroupModal = () => {
  newCatGroupModal.value = true;
};
const side = ref("long");

const formatDate = (date) =>
  date.toLocaleDateString("en-us", {
    day: "2-digit",
    month: "short",
    year: "numeric",
    hourCycle: "h24",
    hour: "2-digit",
    minute: "2-digit",
    second: "2-digit",
  });

const trades = ref([
  {
    id: 192,
    contract: "",
    contractIcon: "",
    contractShort: "ZEC",
    value: 0,
    leverage: 1,
    amount: 0,
    side: "long",
    entry: 0,
    pnl: 0,
    liqPrice: 0,
    stopLoss: 0,
    targetProfit: 0,
    openAt: new Date(),
    closeOn: null,
  },
]);
const totalOrders = ref(trades.value.length);
const newTrade = ref({
  id: 0,
  contract: "",
  contractIcon: "",
  contractShort: "ZEC",
  value: 0,
  leverage: 1,
  amount: 0,
  side: "long",
  entry: 0,
  pnl: 0,
  liqPrice: 0,
  stopLoss: 0,
  targetProfit: 0,
  openAt: formatDate(new Date()),
  closeOn: formatDate(new Date()),
});

const categories = ref([
  {
    title: "All Orders",
    link: "all",
  },
  {
    title: "Long/Buy",
    link: "long",
  },
  {
    title: "Short/Sell",
    link: "short",
  },
]);
const currentCat = ref("all");
const setCurrentCat = (cat) => {
  currentCat.value = cat;
};
</script>
