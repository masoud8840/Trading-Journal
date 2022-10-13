<template>
  <header class="header">
    <h1 class="headline">Orders</h1>
    <h3 class="subheadline">{{ totalOrders }} Orders Found</h3>
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
    <plus v-if="false" class="plus-icon" @click="openNewCatGroupModal" />
  </nav>

  <main>
    <table-header></table-header>
    <trade-item
      v-for="(trade, index) in trades"
      :key="trade"
      :id="trade.id"
      :contract="trade.contract"
      :contract-icon="trade.contractIcon"
      :contract-symbol="trade.contractSymbol"
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
      @update-trade="updateTrade"
    ></trade-item>
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
    @submit-form="addNewTrade"
    btn-label="add to list"
  >
    <form class="add-trade-modal-form">
      <section class="input-group contract-input-container">
        <label for="contract">contract</label>
        <input
          type="text"
          id="contract"
          placeholder="bitcoin"
          v-model.lazy="newTrade.contract"
          @input="searchCurrency"
        />
        <!-- <ul class="contract-search-result">
          <li class="contract-search-result-item" v-for="item in searchResult">
            <span class="name">
              {{ item.name }}
            </span>
          </li>
        </ul> -->
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
      <section class="input-group leverage-input-group">
        <label for="leverage">leverage</label>
        <range-slider v-model="newTrade.leverage"></range-slider>
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
        <input
          type="number"
          id="entry"
          placeholder="10.2"
          v-model="newTrade.entry"
        />
      </section>
      <section class="input-group">
        <label for="st-tp">Stop Loss / Target Profit</label>
        <div>
          <input
            type="number"
            id="st-tp"
            placeholder="9.2"
            v-model="newTrade.stopLoss"
          />
          <span>/</span>
          <input
            type="number"
            id="st-tp"
            placeholder="15.2"
            v-model="newTrade.targetProfit"
          />
        </div>
      </section>
    </form>
  </modal-base>

  <!-- Do not add this yet -->
  <!--
  <modal-base
    modal-title="New Category Group"
    v-if="newCatGroupModal"
    @close-modal="closeModal"
    btn-label="add category"
  >
    <form class="add-trade-modal-form">
      <section class="input-group">
        <label for="name">Contract*</label>
        <input
          type="text"
          id="name"
          placeholder="Long/Buy"
          v-model="newCategory.title"
        />
      </section>
      <section class="input-group">
        <label for="cat-shorthand">short hand</label>
        <input
          type="number"
          id="cat-shorthand"
          placeholder="long"
          v-model="newCategory.link"
        />
      </section>
    </form>
  </modal-base> 
  -->
</template>

<script setup>
// Components
import TableHeader from "./components/layout/TableHeader.vue";
import TradeItem from "./components/UI/TradeItem.vue";
import ModalBase from "./components/UI/ModalBase.vue";
import RangeSlider from "./components/UI/RangeSlider.vue";
// icons
import plus from "./assets/Plus.vue";
import LeftChevron from "./assets/LeftChevron.vue";
import RightChevron from "./assets/RightChevron.vue";

import { ref } from "vue";
import axios from "axios";

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
    contract: "zcash",
    contractIcon: "",
    contractSymbol: "",
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
  contract: "",
  value: 0,
  leverage: 1,
  side: "long",
  entry: 0,
  stopLoss: 0,
  targetProfit: 0,
  openAt: formatDate(new Date()),
  closeOn: formatDate(new Date()),
});

const tradeTrashould = ref({
  left: `${((newTrade.value.leverage - 1) / (100 + 9)) * 100}%`,
});
const addNewTrade = () => {
  const aTrade = {
    id: totalOrders.value++,
    contract: newTrade.value.contract,
    contractIcon: "",
    contractSymbol: "",
    value: newTrade.value.value,
    leverage: newTrade.value.leverage,
    amount: 0,
    side: newTrade.value.side,
    entry: newTrade.value.entry,
    pnl: 0,
    liqPrice: 0,
    stopLoss: newTrade.value.stopLoss,
    targetProfit: newTrade.value.targetProfit,
    openAt: new Date(),
    closeOn: null,
  };
  trades.value.unshift(aTrade);
  console.log("added");
};

const updateTrade = (value) => {
  const updatedTrade = trades.value.map((t) => {
    if (t.id === value.id) {
      t.contractIcon = value.contractIcon;
      t.contractSymbol = value.contractSymbol;
    }
  });
};
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
const newCategory = ref({
  title: "",
  link: "",
});
const currentCat = ref("all");
const setCurrentCat = (cat) => {
  currentCat.value = cat;
};

// Search Currency in the Modal
const searchCurrency = async () => {
  await axios.get(`https://cryptingup.com/api/assets/btc`).then((res) => {
    console.log(res.data);
  });
};
const searchResult = ref([]);
</script>
