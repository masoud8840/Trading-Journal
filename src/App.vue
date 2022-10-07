<template>
  <header>
    <h1 class="headline">Orders</h1>
    <h3 class="subheadline">{{ totalOrders }} orders found</h3>
  </header>

  <section class="navigation">
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
    <plus class="plus-icon" />
  </section>

  <table-header></table-header>
  <trade-item
    v-for="trade in trades"
    :id="trade.id"
    :contract="trade.contract"
    :value="trade.value"
    :leverage="trade.leverage"
    :amount="trade.amount"
    :side="trade.side"
    :entry="trade.entry"
    :pnl="trade.pnl"
    :liq-price="trade.liqPrice"
    :sl-tp="trade.slTp"
    :open-at="trade.openAt"
    :close-on="trade.closeOn"
  ></trade-item>

  <section class="pagination">
    <plus class="plus-icon" />
    <div>
      <LeftChevron class="left-chevron" />
      <input type="number" name="page" id="page" value="15" />
      <span>of 192</span>
      <RightChevron class="right-chevron" />
    </div>
  </section>
</template>

<script setup>
import TableHeader from "./components/layout/TableHeader.vue";
import TradeItem from "./components/UI/TradeItem.vue";
// icons
import plus from "./assets/Plus.vue";
import LeftChevron from "./assets/LeftChevron.vue";
import RightChevron from "./assets/RightChevron.vue";
import { ref } from "vue";
const totalOrders = ref(21);
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
    contract: "ZEC",
    value: 120,
    leverage: 10,
    amount: 2.5,
    side: "long",
    entry: 2.15,
    pnl: 0,
    liqPrice: 10,
    slTp: "51/84",
    openAt: formatDate(new Date()),
    closeOn: formatDate(new Date()),
  },
]);
</script>
