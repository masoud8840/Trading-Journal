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
      v-for="(trade, index) in tradesOutput"
      :key="trade"
      :id="trade.id"
      :contract="trade.contract"
      :contract-icon="trade.contractIcon"
      :contract-name="trade.contractName"
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
      :status="trade.status"
      @update-trade="updateTrade"
      @click="closeTrade(index)"
    ></trade-item>
  </main>

  <section class="pagination">
    <plus class="plus-icon" @click="openNewTradeModal" />
    <div>
      <LeftChevron class="left-chevron" />
      <input type="number" name="page" id="page" value="1" />
      <span>of 1</span>
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
      <section class="input-group">
        <label for="contract">contract</label>
        <input
          type="text"
          id="contract"
          placeholder="BTC"
          v-model.lazy="newTrade.contract"
          @input="searchCurrency"
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
      <section class="input-group leverage-input-group">
        <label for="leverage">leverage</label>
        <range-slider v-model.number="newTrade.leverage"></range-slider>
      </section>
      <section class="input-group">
        <label for="side">Side</label>
        <div class="row">
          <button
            type="button"
            :class="{ 'active-long': newTrade.side === 'long' }"
            @click="setNewTradeSide('long')"
          >
            Buy
          </button>
          <button
            type="button"
            :class="{ 'active-short': newTrade.side === 'short' }"
            @click="setNewTradeSide('short')"
          >
            Sell
          </button>
        </div>
      </section>
      <section class="input-group row">
        <div class="col">
          <label for="entry">entry price</label>
          <input
            type="number"
            id="entry"
            placeholder="10.2"
            v-model="newTrade.entry"
          />
        </div>
        <div class="col">
          <label for="sl-tp">Stop Loss / Target Profit</label>
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
        </div>
      </section>
      <section class="error" v-if="modalError">
        <p class="modal-error">Recheck all available empty inputs!</p>
      </section>
    </form>
  </modal-base>
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

import { ref, computed } from "vue";

const modalError = ref(false);
const newTradeModal = ref(false);
// comments will use in next version of app
// const newCatGroupModal = ref(false);
const closeModal = () => {
  newTradeModal.value = false;
  // newCatGroupModal.value = false;
  modalError.value = false;
};
const openNewTradeModal = () => {
  newTradeModal.value = true;
};
// const openNewCatGroupModal = () => {
//   newCatGroupModal.value = true;
// };

const categories = ref([
  {
    title: "All Orders",
    link: "all",
  },
  {
    title: "Buy",
    link: "long",
  },
  {
    title: "Sell",
    link: "short",
  },
]);
// const newCategory = ref({
//   title: "",
//   link: "",
// });
const currentCat = ref("all");
const setCurrentCat = (cat) => {
  currentCat.value = cat;
};
const trades = ref([]);
const tradesOutput = computed(() => {
  if (currentCat.value === "all") {
    return trades.value;
  } else {
    const newTrades = trades.value.filter((t) => t.side === currentCat.value);
    return newTrades;
  }
});
const totalOrders = ref(0);
const newTrade = ref({
  contract: "",
  value: null,
  leverage: 1,
  side: "long",
  entry: null,
  stopLoss: null,
  targetProfit: null,
  openAt: new Date(),
  closeOn: null,
  status: "open",
});

const addNewTrade = () => {
  if (
    newTrade.value.contract.trim() === "" ||
    newTrade.value.value === null ||
    newTrade.value.entry === null ||
    newTrade.value.stopLoss === null ||
    newTrade.value.targetProfit === null
  ) {
    modalError.value = true;
    return;
  }
  modalError.value = false;
  const aTrade = {
    id: ++totalOrders.value,
    contract: newTrade.value.contract,
    contractIcon: "",
    contractName: "",
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
    status: newTrade.status,
  };
  trades.value.unshift(aTrade);

  newTrade.value = {
    contract: "",
    value: null,
    leverage: 1,
    side: "long",
    entry: null,
    stopLoss: null,
    targetProfit: null,
    openAt: new Date(),
    closeOn: null,
    status: "open",
  };
  newTradeModal.value = false;

  localStorage.setItem("trades", JSON.stringify(trades.value));
};

const updateTrade = (value) => {
  const updatedTrade = trades.value.map((t) => {
    if (t.id === value.id) {
      t.contractIcon = value.contractIcon;
      t.contractSymbol = value.contractSymbol;
    }
  });
};

const closeTrade = (index) => {
  if (trades.value[index].status === "closed") return;
  trades.value[index].status = "closed";
  trades.value[index].closeOn = new Date();
  const closedTrade = trades.value[index];
  trades.value.splice(index, 1);
  trades.value.push(closedTrade);
};
const setNewTradeSide = (side) => {
  newTrade.value.side = side;
};
// Search Currency in the Modal
// const searchCurrency = async () => {
//   await axios
//     .get(`http://rest.coinapi.io/v1/assets/${newTrade.value.contract}`, {
//       headers: {
//         "X-CoinAPI-Key": "CBFD70D0-DA62-4385-9A8B-6A94B83F5C69",
//       },
//     })
//     .then((res) => {
//       console.log(res.status);
//     });
// };
// const searchResult = ref([]);
</script>
