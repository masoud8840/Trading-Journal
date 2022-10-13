<template>
  <div class="slider-container">
    <span class="bar"
      ><span
        class="fill"
        :style="{
          width: `${((props.modelValue - 1) / (100 - 1)) * 100}%`,
          backgroundImage: `linear-gradient(120deg, rgb(56, 239, 125), rgb(239, 71, 58));`,
        }"
      ></span
    ></span>
    <input
      type="range"
      class="slider"
      min="1"
      max="100"
      step="1"
      v-model="value"
      @input="updateValue"
    />
    <span class="ruler">
      <span v-for="num in 28"></span>
    </span>
    <span class="level" :style="{ left: `${((value - 1) / (100 + 5)) * 100}%` }"
      >x{{ value }}</span
    >
  </div>
</template>

<script setup>
import { ref } from "vue";
const props = defineProps(["modelValue"]);
const emits = defineEmits(["update:modelValue"]);
const value = ref(1);

const updateValue = () => {
  emits("update:modelValue", value.value);
  const calculatedValue = ((value.value - 1) / (100 - 1)) * 100;
  document.querySelector(
    "span.fill"
  ).style.backgroundImage = `linear-gradient(to right, #38ef7dbf, ${
    100 - calculatedValue
  }%, #ef473abf)`;
};

const calPercent = (s, e, p) => {
  return (e - s) * (p / 100) + s;
};
</script>

<style lang="scss" scoped>
.slider-container {
  width: 100%;
  position: relative;
  margin-bottom: 45px;
  input {
    appearance: none;
    width: 100%;
    height: 10px;
    width: 100% !important;
    background-color: transparent;
    z-index: 10;
    outline: 0;
    &::-webkit-slider-thumb {
      appearance: none;
      -webkit-appearance: none;
      width: 30px;
      height: 30px;
      border: 5px solid var(--darkRedGradient);
      border-radius: 15px;
      background-color: #fff;
      transition: 0.05s ease-in-out;
      &:active {
        border-width: 2px;
      }
    }

    &::-webkit-slider-runnable-track {
      appearance: none;
      -webkit-appearance: none;
      background-color: transparent;
    }
  }
  span.bar {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    height: 10px;
    width: 100%;
    background-color: #e9e9e9;
    border-radius: 15px;
  }
  span.fill {
    position: absolute;
    left: 0;
    border-radius: 15px;

    z-index: 10;
    // background-color: rgba($color: #11998e, $alpha: 0.15);
    height: 10px;
  }
  span.level {
    position: absolute;
    top: 40px;
    color: var(--semilightText);
    font-size: 14px;
    padding: 0 5px;
  }
  span.ruler {
    height: 37px;
    position: absolute;
    top: 23px;
    left: 0;
    right: 0;
    display: flex;
    justify-content: space-between;
    padding: 0 14px;
    span {
      height: 12px;
      width: 1px;
      background-color: #767676;
    }
  }
}
</style>
