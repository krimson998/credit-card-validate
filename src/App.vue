<template>
  <div class="container">
    <header class="header">
      <img src="@/assets/crediton.svg" alt="logo-icon" />
    </header>
    <h1 class="title">Добавление платежной карты</h1>
    <div class="card-container">
      <div class="form-container">
        <form>
          <label for="card-number" class="card-number-title">Номер карты</label>
          <input
            type="text"
            name="card-number"
            class="card-number-input"
            placeholder="Номер карты"
            v-mask="'#### #### #### ####'"
            v-model="cardNumberInput"
            required
          />
          <div class="card-type-img-container">
            <img class="card-type-img" :src="imgURL" :alt="imgAlt" />
          </div>

          <div
            v-if="!valid_credit_card(cardNumberInput) && hidden"
            class="validation-error"
          >
            * Ошибка валидации или поле пустое
          </div>

          <div class="small-inputs-container">
            <input
              type="text"
              name="card-date"
              class="card-date-input"
              placeholder="Срок действия"
              v-mask="mask"
              v-model="cardDateInput"
              required
            />
            <input
              type="password"
              name="card-cvc"
              class="card-cvc-input"
              placeholder="CVC код"
              v-mask="'###'"
              v-model="cardCvcInput"
              required
            />
          </div>
          <div class="error-container">
            <div
              v-if="!isFutureDate(cardDateInput) && hidden"
              class="validation-error"
            >
              * Поле пустое, или истек срок действия карты
            </div>
            <div
              v-if="emptyCVV(cardCvcInput) && hidden"
              class="validation-error"
            >
              * Поле пустое или заполнено не до конца
            </div>
          </div>
          <p class="payment-description">
            Для привязки карты мы проведем платеж в размере 1.00 UAH, который
            будет возвращен в течении 30 минут.
          </p>
          <button
            :disabled="clicked"
            type="button"
            class="get-money-button"
            @click.prevent="cardValidation"
          >
            <span class="loader" v-show="toggleSpinner"></span
            ><span class="get-money-title">Получить деньги</span>
          </button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export function cardExpirationMask(value) {
  const month = [
    /[0-1]/,
    value.charAt(0) === '1'
      ? /[0-2]/
      : /[0-9]/ || value.charAt(0) === '0'
      ? /[1-9]/
      : /[0-9]/,
  ];
  const year = [/[0-9]/, value.charAt(3) === '0' ? /[1-9]/ : /[0-9]/];

  return [...month, '/', ...year];
}

import creditCardType from 'credit-card-type';

export default {
  name: 'App',
  components: {},
  data() {
    return {
      mask: cardExpirationMask,
      cardNumberInput: '',
      cardDateInput: '',
      cardCvcInput: '',
      hidden: false,
      clicked: false,
      toggleSpinner: false,
    };
  },
  methods: {
    creditCardType(value) {
      let cardType = creditCardType(value);
      if (cardType[0] === undefined) {
        return;
      }
      if (cardType[0] === '') {
        return null;
      }
      if (this.cardNumberInput.length === 0) {
        return null;
      }
      return cardType[0].type;
    },
    valid_credit_card(value) {
      // Accept only digits, dashes or spaces
      if (/[^0-9-\s]+/.test(value)) return false;
      if (!value) return false;

      // The Luhn Algorithm. It's so pretty.
      let nCheck = 0,
        bEven = false;
      value = value.replace(/\D/g, '');

      for (var n = value.length - 1; n >= 0; n--) {
        var cDigit = value.charAt(n),
          nDigit = parseInt(cDigit, 10);

        if (bEven && (nDigit *= 2) > 9) nDigit -= 9;

        nCheck += nDigit;
        bEven = !bEven;
      }

      return nCheck % 10 == 0;
    },
    isFutureDate(idate) {
      let today = new Date().getTime();
      idate = idate.split('/');

      idate = new Date('20' + idate[1], idate[0] - 1, 0).getTime();
      return today - idate < 0;
    },
    emptyCVV(value) {
      if (value.length < 3) {
        return true;
      } else {
        return false;
      }
    },
    cardValidation() {
      this.hidden = true;
      this.clicked = true;
      this.toggleSpinner = true;
      if (
        this.valid_credit_card(this.cardNumberInput) === true &&
        this.isFutureDate(this.cardDateInput) === true &&
        this.emptyCVV(this.cardCvcInput) === false
      ) {
        setTimeout(() => {
          this.clicked = false;
          this.toggleSpinner = false;
          return alert('verified');
        }, 3000);
      } else {
        setTimeout(() => {
          this.clicked = false;
          this.toggleSpinner = false;
        }, 3000);
      }
    },
  },
  computed: {
    imgURL() {
      if (!this.creditCardType(this.cardNumberInput)) {
        return require(`@/assets/credit-card-brands/credit-cards.svg`);
      }
      let typeName = this.creditCardType(this.cardNumberInput);
      return require(`@/assets/credit-card-brands/${typeName}.svg`);
    },
    imgAlt() {
      return this.creditCardType(this.cardNumberInput);
    },
  },
};
</script>

<style lang="scss">
$core-sans-light: 'Corse Sans G light', sans-serif;
$core-sans-regular: 'Corse Sans G regular', sans-serif;
$core-sans-thin: 'Corse Sans G thin', sans-serif;
$gradient: linear-gradient(90deg, #5a4be6 0.26%, #73aff7 47.1%, #93d0d9 99.8%);
$form-indent: 17px;
$form-padding: 1rem;
$paragraph-size: 16px;
$button-gradient-active: linear-gradient(
  122.5deg,
  #5a4be6 -33.07%,
  #73aff7 48.35%,
  #93d0d9 139.94%
);
$button-gradient-disabled: linear-gradient(
  122.5deg,
  #b0b0b0 -33.07%,
  #4b88d0 48.35%,
  #93d0d9 139.94%
);

@font-face {
  font-family: 'Core Sans G regular';
  src: local('Core Sans G regular'),
    url(./assets/fonts/CoreSansG-Regular.ttf) format('truetype');
}
@font-face {
  font-family: 'Core Sans G thin';
  src: local('Core Sans G thin'),
    url(./assets/fonts/CoreSansG-Thin.ttf) format('truetype');
}
@font-face {
  font-family: 'Core Sans G light';
  src: local('Core Sans G light'),
    url(./assets/fonts/CoreSansG-Thin.ttf) format('truetype');
}

body {
  margin: 0;
  font-family: $core-sans-regular;
}

.header {
  width: 100%;
  height: 100px;
  display: flex;
  justify-content: center;
  background: $gradient;
}
.container {
  display: flex;
  flex-direction: column;
}
.title {
  font-family: $core-sans-thin;
  height: 34px;
  margin-left: auto;
  margin-right: auto;
  margin-top: 80px;
  color: #000000;
  font-weight: 400;
}
.card-container {
  width: 66.666666%;
  min-height: 100%;
  height: 530px;
  border: 1px solid #e4e4e4;
  border-radius: 4px;
  margin-left: auto;
  margin-right: auto;
  margin-top: 20px;
  display: flex;
  margin-bottom: 130px;
}
.form-container {
  width: 400px;
  margin-right: auto;
  margin-left: auto;

  margin-top: auto;
  margin-bottom: auto;
  height: 330px;
}
.card-number-title {
  font-size: 18px;
  font-weight: bold;
  width: 100%;
  display: inline-block;
}
.card-number-input {
  width: 93%;
  border-radius: 4px;
  border: 1px solid #cccccc;
  height: 55px;
  font-size: $paragraph-size;
  padding-left: $form-padding;
  padding-right: $form-padding;
  margin-top: $form-indent;
  position: relative;
}
.card-type-img-container {
  position: relative;
}

.card-type-img {
  position: absolute;
  bottom: 15px;
  left: 340px;
  width: 42px;
  height: 28px;
}
.card-date-input {
  width: 41%;
  border-radius: 4px;
  border: 1px solid #cccccc;
  height: 55px;
  font-size: $paragraph-size;
  padding-left: $form-padding;
  padding-right: $form-padding;
  margin-top: $form-indent;
}
.card-cvc-input {
  width: 41%;
  border-radius: 4px;
  border: 1px solid #cccccc;
  height: 55px;
  font-size: $paragraph-size;
  padding-left: $form-padding;
  padding-right: $form-padding;
  margin-top: $form-indent;

  &:focus {
  }
}

.small-inputs-container {
  display: flex;
  width: 100%;
  justify-content: space-between;
}
.payment-description {
  color: #808080;
  font-size: $paragraph-size;
  line-height: 20px;
  font-family: $core-sans-light;
  font-weight: 100;
}
.get-money-button {
  background: $button-gradient-active;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 56px;
  width: 100%;
}
.get-money-title {
  font-size: $paragraph-size;
  color: #ffffff;
  margin-left: 7px;
}
.validation-error {
  color: #ff8d8d;
  font-size: 12px;
  line-height: 167%;
  margin-top: 10px;
  width: 50%;
}
.validation-accept {
  color: lightgreen;
  font-size: 12px;
  line-height: 167%;
  margin-top: 10px;
}
.error-container {
  width: 100%;
  display: flex;
  flex-direction: row;
}
.loader {
  border: 4px solid #f3f3f3; /* Light grey */
  border-top: 4px solid #3498db; /* Blue */
  border-radius: 50%;
  width: 13px;
  height: 13px;
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
