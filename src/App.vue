<template>
  <form action="" class="form">
    <h2 class="form__title">Рассчитайте стоимость автомобиля в лизинг</h2>
    <div class="form__values-wrap values" :class="{ disabled: isDisabled }">
      <div class="values__item">
        <p class="values__label">Стоимость автомобиля</p>
        <div class="values__input-wrap">
          <input type="number" class="values__input" v-model="carCost" @blur="checkCarPrice" :disabled="isDisabled">
          <p class="values__text">₽</p>
          <input type="range" min="1000000" max="6000000" step="1" class="values__range slider-progress"
            v-model="carCost" :disabled="isDisabled">
        </div>
      </div>
      <div class="values__item">
        <p class="values__label">Первоначальный взнос</p>
        <div class="values__input-wrap">
          <input type="number" class="values__input" :value="initialPayment" disabled>
          <input type="number" class="values__text values__text--dec" v-model="initialPaymentPercent"
            @blur="checkPercent">
          <input type="range" min="10" max="60" step="1" class="values__range slider-progress"
            v-model="initialPaymentPercent" :disabled="isDisabled">
        </div>
      </div>
      <div class="values__item">
        <p class="values__label">Срок лизинга</p>
        <div class="values__input-wrap">
          <input type="number" class="values__input" v-model="leaseTerm" @blur="checkMonths" :disabled="isDisabled">
          <p class="values__text">мес.</p>
          <input type="range" min="1" max="60" step="1" class="values__range slider-progress" v-model="leaseTerm"
            :disabled="isDisabled">
        </div>
      </div>
    </div>

    <div class="form__result-wrap result">
      <div class="result__item">
        <p class="result__label">Сумма договора лизинга</p>
        <p class="result__text">{{totalSum}} ₽</p>
      </div>
      <div class="result__item">
        <p class="result__label">Ежемесячный платеж от</p>
        <p class="result__text">{{mounthlyPaymentFrom}} ₽</p>
      </div>
      <div class="result__item">
        <button class="result__btn" @click.prevent="sendData" :disabled="isDisabled">Оставить заявку</button>
      </div>

    </div>
  </form>
</template>

<script>
export default {
  data() {
    return {
      carCost: 3300000,
      initialPaymentPercent: 13,
      leaseTerm: 60,
      isDisabled: false,
    }
  },

  computed: {
    initialPayment: function () {
      return Math.round((this.initialPaymentPercent / 100) * this.carCost);
    },
    totalSum: function () {
      return this.initialPayment + this.leaseTerm * this.mounthlyPaymentFrom;
    },
    mounthlyPaymentFrom: function () {
      return Math.round((this.carCost - this.initialPayment) * (((this.initialPaymentPercent / 100) * Math.pow((1 + (this.initialPaymentPercent / 100)), this.leaseTerm)) / (Math.pow((1 + (this.initialPaymentPercent / 100)), this.leaseTerm) - 1)));
    },
    dataToSend: function () {
      return {
        car_cost: this.carCost,
        initial_payment: this.initialPayment,
        initial_payment_percent: this.initialPaymentPercent,
        lease_term: this.leaseTerm,
        total_sum: this.totalSum,
        mounthly_payment_from: this.mounthlyPaymentFrom
      };
    },
  },

  methods: {
    checkCarPrice() {
      if (this.carCost < 1000000) {
        this.carCost = 1000000;
      } else if (this.carCost > 6000000) {
        this.carCost = 6000000;
      }
    },

    checkPercent() {
      if (this.initialPaymentPercent < 1) {
        this.initialPaymentPercent = 1;
      } else if (this.initialPaymentPercent > 60) {
        this.initialPaymentPercent = 60;
      }
    },

    checkMonths() {
      if (this.leaseTerm < 1) {
        this.leaseTerm = 1;
      } else if (this.leaseTerm > 60) {
        this.leaseTerm = 60;
      }
    },

    async sendData() {
      this.isDisabled = true;

      let response = await fetch('https://hookb.in/eK160jgYJ6UlaRPldJ1P', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(this.dataToSend),
      });
      this.isDisabled = false;

      let result = await response.json();
      console.log(result.success);
      return result.success;
    },
  },
}
</script>

<style>

</style>