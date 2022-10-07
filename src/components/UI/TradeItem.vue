<template>
  <section class="trade-item">
    <div class="id">
      <p>#{{ props.id }}</p>
    </div>
    <div class="contract">
      <img :src="`/images/${props.contract}.svg`" alt="contract" />
      <p>{{ props.contract }}</p>
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
      <p>{{ props.slTp }}</p>
    </div>
    <div class="open-at">
      <p>{{ props.openAt }}</p>
    </div>
    <div class="close-on">
      <p>{{ props.closeOn }}</p>
    </div>
  </section>
</template>

<script setup>
const props = defineProps({
  id: {
    type: Number,
    default: -1,
  },
  contract: {
    type: String,
    defaut: "undefined",
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
  liqPrice: {
    type: Number,
    default: -1,
  },
  slTp: {
    type: String,
    default: "NULL",
  },
  openAt: {
    type: Date,
    default: new Date(),
  },
  closeOn: {
    type: Date,
    default: new Date(),
  },
});
</script>
