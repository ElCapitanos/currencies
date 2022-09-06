<template>
  <div class="currencies__all">
    <h2 class="currencies__title">{{ title }}</h2>

    <div class="currencies__input-container">
      <span class="currencies__triangle" @click="openList()">&#9660;</span>
      <input
        type="text"
        ref="currencyListInput"
        class="currencies__input"
        :placeholder="placeholder"
        @click="openList()"
      />
    </div>
    <ul class="currencies__list" v-if="listFlag">
      <li
        v-for="(item, index) in dataCurrency.value"
        :key="index"
        class="currencies__currency"
        @click="newCurrencyInInput(item, index)"
      >
        <span> {{ item.CharCode }}&nbsp;—&nbsp;{{ item.Name }} </span>
      </li>
    </ul>
  </div>
</template>
<script setup>
import { onMounted, reactive, ref } from "vue";
const props = defineProps(["title", "placeholder"]);
const dataCurrency = reactive([]);
let currencyListInput = ref(null);
let listFlag = ref(false);
const emit = defineEmits(["inputValue"]);
const openList = () => {
  listFlag.value = !listFlag.value;
};
const fetchCurrency = () => {
  fetch("https://www.cbr-xml-daily.ru/daily_json.js")
    .then((res) => res.json())
    .then((data) => {
      dataCurrency.value = data.Valute;
    });
};
const newCurrencyInInput = (item, index) => {
  currencyListInput.value.value = index;
  emit("inputValue", item);
  listFlag.value = false;
};
onMounted(() => fetchCurrency(dataCurrency.value));
</script>
<style scoped lang="scss">
.currencies__title {
  padding: 0;
  margin: 0 0 10px 0;
  font-size: 16px;
  font-weight: 200;
  text-transform: uppercase;
  font-family: "Roboto Serif";
    @media screen and (max-width: 1001px) {
    margin: 0 auto 5px;
  }
}
.currencies__currency {
  display: flex;
  cursor: pointer;
  &:hover {
    background: #ccc;
  }
}
.currencies__input {
  outline: none;
  padding-left: 20px;
  border: solid 1px #ccc;
  width: 100%;
}
.currencies__list {
  list-style-type: none;
  display: flex;
  flex-direction: column;
  border: solid 1px #ccc;
  border-top: 3px solid #fff;
  padding: 5px 0 0 20px;
  margin-top: -3px;
  width: 100%;
  max-height: 300px;
  overflow-y: auto;
  scrollbar-width: thin;
  scrollbar-color: #000 #ccc;
  &::-webkit-scrollbar {
    height: 12px;
    width: 2px;
  }
  &::-webkit-scrollbar-track {
    background: #ccc;
  }
  &::-webkit-scrollbar-thumb {
    background-color: #000;
    border: 2px solid #000;
  }
}
.currencies__current {
  position: relative;
}
.currencies__triangle {
  position: absolute;
  cursor: pointer;
  width: 18px;
  height: 18px;
  right: 10px;
  top: 2px;
  transition: 0.4s linear;
  &:hover {
    transform: scale(1.1);
  }
}

.currencies__current,
.currencies__all {
  display: flex;
  flex-direction: column;
  width: 300px;
  padding: 20px;
  box-sizing: border-box;
}
.currencies__input-container {
  position: relative;
}
* {
  box-sizing: border-box;
}
</style>
