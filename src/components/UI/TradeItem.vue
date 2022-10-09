<template>
  <section class="trade-item">
    <div class="id">
      <p>#{{ props.id }}</p>
    </div>
    <div class="contract">
      <img :src="props.contractIcon" alt="contract" />
      <p>{{ props.contractShort }}</p>
    </div>
    <div class="value">
      <p>{{ props.value }}</p>
    </div>
    <div class="leverage">
      <p>x{{ props.leverage }}</p>
    </div>
    <div class="amount">
      <p>{{ props.amount }}</p>
    </div>
    <div class="side">
      <p :class="props.side">{{ props.side }}</p>
    </div>
    <div class="entry">
      <p>{{ props.entry }}</p>
    </div>
    <div class="pnl">
      <p>{{ props.pnl }}</p>
    </div>
    <div class="liq-price">
      <p>{{ props.liqPrice }}</p>
    </div>
    <div class="sl-tp">
      <p>{{ `${props.stopLoss}/${props.targetProfit}` }}</p>
    </div>
    <div class="open-at">
      <p>{{ formatDate(props.openAt) }}</p>
    </div>
    <div class="close-on">
      <span>{{
        props.closeOn === null ? "----" : formatDate(props.closeOn)
      }}</span>
    </div>
  </section>
</template>

<script setup>
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

const props = defineProps({
  id: {
    type: Number,
    default: -1,
  },
  contract: {
    type: String,
    defaut: "NULL",
  },
  contractIcon: {
    type: String,
    defaut: "NULL",
  },
  contractShort: {
    type: String,
    default: "NULL",
  },
  value: {
    type: Number,
    default: -1,
  },
  leverage: {
    type: Number,
    default: -1,
  },
  amount: {
    type: Number,
    default: -1,
  },
  side: {
    type: String,
    defaut: "NULL",
  },
  entry: {
    type: Number,
    default: -1,
  },
  pnl: {
    type: Number,
    default: -1,
  },
  liqPrice: {
    type: Number,
    default: -1,
  },
  stopLoss: {
    type: Number,
    default: -1,
  },
  targetProfit: {
    type: Number,
    default: -1,
  },
  openAt: {
    type: Date,
    default: new Date(),
  },
  closeOn: {
    type: Date,
    default: null,
  },
});
// const pricesWs = new WebSocket(`wss://ws.coincap.io/prices?assets=zcash`);

// console.log("we are establishing websocket");
// pricesWs.onmessage = function (msg) {
//   console.log(JSON.parse(msg.data[props.contract]));
//   console.log("we established");
// };
</script>
