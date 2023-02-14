<template>
  <form action="" class="form">
    <h2 class="form__title">Рассчитайте стоимость автомобиля в лизинг</h2>

    <div class="form__values-wrap values">
      <div class="values__item">
        <p class="values__label">Стоимость автомобиля</p>
        <div class="values__input-wrap">
          <input type="number" class="values__input values__input--cost" v-model.lazy="carCost" @blur="checkCarCost">
          <p class="values__text values__text--cost">{{
            parseInt(carCost, 10).toLocaleString()
          }}</p>
          <p class="values__text">₽</p>
          <input type="range" :min="carCostMin" :max="carCostMax" step="1" class="values__range slider-progress"
            v-model="carCost">
        </div>
      </div>

      <div class="values__item">
        <p class="values__label">Первоначальный взнос</p>
        <div class="values__input-wrap">
          <input type="number" class="values__input values__input--rub" v-model.lazy="initialPayment"
            @blur="checkinitialPayment">
          <p class="values__text values__text--rub">{{
            initialPayment.toLocaleString() + ' ₽'
          }}</p>
          <span class="values__text values__text--dec">{{ initialPaymentPercent + '%' }}</span>
          <input type="range" :min="initialPaymentPercentMin" :max="initialPaymentPercentMax" step="1"
            class="values__range slider-progress" v-model="initialPaymentPercent">
        </div>
      </div>

      <div class="values__item">
        <p class="values__label">Срок лизинга</p>
        <div class="values__input-wrap">
          <input type="number" class="values__input" v-model.lazy="leaseTerm" @blur="checkMonths">
          <p class="values__text">мес.</p>
          <input type="range" min="6" max="120" step="1" class="values__range slider-progress" v-model="leaseTerm">
        </div>
      </div>
    </div>

    <div class="form__result-wrap result">
      <div class="result__item">
        <p class="result__label">Сумма договора лизинга</p>
        <p class="result__text">{{ totalSum.toLocaleString() }} ₽</p>
      </div>
      <div class="result__item">
        <p class="result__label">Ежемесячный платеж от</p>
        <p class="result__text">{{ mounthlyPaymentFrom.toLocaleString() }} ₽</p>
      </div>
      <div class="result__item result__item--btn">
        <button class="result__btn" @click.prevent="sendData">Оставить заявку</button>
      </div>

    </div>
  </form>
</template>

<script>
export default {
  data() {
    return {
      carCost: 3300000,
      carCostMin: 1500000,
      carCostMax: 10000000,
      initialPaymentPercent: 13,
      initialPaymentPercentMin: 10,
      initialPaymentPercentMax: 60,
      leaseTerm: 60,
      leaseTermMin: 6,
      leaseTermMax: 120,
    }
  },

  computed: {
    initialPayment: {
      get() {
        return Math.round((this.initialPaymentPercent / 100) * this.carCost);
      },
      set(value) {
        this.initialPaymentPercent = Math.round(value * 100 / this.carCost);
      }
    },

    initialPaymentMin: function () {
      return Math.round((this.initialPaymentPercentMin / 100) * this.carCost);
    },

    initialPaymentMax: function () {
      return Math.round((this.initialPaymentPercentMax / 100) * this.carCost);
    },

    totalSum: function () {
      return +this.initialPayment + this.leaseTerm * this.mounthlyPaymentFrom;
    },

    mounthlyPaymentFrom: function () {
      return Math.round((this.carCost - this.initialPayment) * (0.05 * Math.pow((1 + 0.05), this.leaseTerm) / (Math.pow((1 + 0.05), this.leaseTerm) - 1)));
    },

    dataToSend: function () {
      return {
        carСost: this.carCost,
        initialPayment: this.initialPayment,
        initialPaymentPercent: this.initialPaymentPercent,
        leaseTerm: this.leaseTerm,
        totalSum: this.totalSum,
        mounthlyPaymentFrom: this.mounthlyPaymentFrom
      };
    },
  },

  methods: {
    checkCarCost() {
      if (this.carCost < this.carCostMin) {
        this.carCost = this.carCostMin;
      } else if (this.carCost > this.carCostMax) {
        this.carCost = this.carCostMax;
      }
    },

    checkinitialPayment() {
      if (this.initialPayment < this.initialPaymentMin) {
        this.initialPayment = this.initialPaymentMin;
      } else if (this.initialPayment > this.initialPaymentMax) {
        this.initialPayment = this.initialPaymentMax;
      }
    },

    checkMonths() {
      if (this.leaseTerm < 6) {
        this.leaseTerm = 6;
      } else if (this.leaseTerm > 120) {
        this.leaseTerm = 120;
      }
    },

    sendData() {
      alert(JSON.stringify(this.dataToSend));
    },
  },
}
</script>

<style>

</style>