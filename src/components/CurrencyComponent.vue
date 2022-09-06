<template>
  <div class="currencies">
    <div class="currencies__part">
      <input
        type="text"
        placeholder="Введите сумму"
        ref="baseInput"
        class="currencies__input"
        autofocus
        @input="letsCalc()"
      />
      <CurrencyList
        :title="`Базовая валюта:`"
        :placeholder="currentCurrency || 'Валюта не определена (RUB)'"
        @inputValue="inputValueBase($event)"
      />
    </div>
    <div class="currencies__part">
      <span ref="targetInput" class="currencies__input"></span>
      <CurrencyList
        :title="`Целевая валюта:`"
        placeholder="Выберите валюту"
        @inputValue="inputValueTarget($event)"
      />
    </div>
  </div>
</template>

<script setup>
import CurrencyList from "@/components/CurrencyList.vue";
import { onMounted, ref } from "vue";
const currentCurrency = ref("");
let baseInput = ref(null);
let targetInput = ref(null);
let baseCurrency = ref("");
let targetCurrency = ref("");
let baseCurrencyValue = ref("");
let targetCurrencyValue = ref("");
const inputValueBase = (item) => {
  baseCurrency.value = item.CharCode;
  baseCurrencyValue = item.Value.toFixed(2);
};
const inputValueTarget = (item) => {
  targetCurrency = item.CharCode;
  targetCurrencyValue = item.Value.toFixed(2);
};
const getLocalCurrency = () => {
  fetch(
    // "https://ipgeolocation.abstractapi.com/v1/?api_key=0e80ef58ae9847aa9e71eecc902d9a15"
    "http://www.geoplugin.net/json.gp"
  )
    .then((res) => res.json())
    .then((data) => {
      currentCurrency.value = data.geoplugin_currencyCode;
    });
};
const letsCalc = () => {
  if (!baseCurrency.value) {
    let actualValue = (baseInput.value.value / targetCurrencyValue).toFixed(2);
    !isNaN(actualValue)
      ? (targetInput.value.textContent = actualValue)
      : (targetInput.value.textContent = "Ошибка");
  } else {
    let actualValue = (
      baseInput.value.value *
      (baseCurrencyValue / targetCurrencyValue)
    ).toFixed(2);
    !isNaN(actualValue)
      ? (targetInput.value.textContent = actualValue)
      : (targetInput.value.textContent = "Ошибка");
  }
};
onMounted(() => {
  getLocalCurrency();
});
</script>

<style scoped lang="scss">
.currencies__input {
  height: 21px;
  outline: none;
  display: flex;
  border: solid 1px #ccc;
  margin-top: 48px;
  padding-left: 10px;
  width: 200px;
  line-height: 21px;
  @media screen and (max-width: 1001px) {
    margin: 48px auto 0;
  }
}
.currencies__part {
  display: flex;
  flex-direction: row;
  justify-content: center;
  padding: 0 0 0 20px;
  &:first-child {
    border-right: 1px solid #ccc;
    @media screen and (max-width: 1001px) {
      border: none;
    }
  }
  @media screen and (max-width: 1001px) {
    flex-direction: column;
    margin: 0 auto;
  }
}
.currencies {
  display: flex;
  flex-direction: row;
  margin: 100px auto 0;
  border-radius: 6px;
  font-family: "Roboto";
  font-size: 14px;
  justify-content: center;
  @media screen and (max-width: 1001px) {
    border: solid 1px #ccc;
  }
  @media screen and (max-width: 628px) {
    flex-direction: column;
  }
}
* {
  box-sizing: border-box;
}
</style>
