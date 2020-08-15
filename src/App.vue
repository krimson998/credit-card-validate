<template>
  <div class="container">
    <header class="header">
      <img class="logo-header" src="@/assets/crediton.svg" alt="logo-icon" />
      <div class="language">
        <span class="ru-language">РУ </span>
        <span class="horizontal-line">|</span>УКР
      </div>
    </header>
    <h1 class="title">Добавление платежной карты</h1>
    <div class="main-container">
      <div class="card-container">
        <div class="form-container">
          <form>
            <label for="card-number" class="card-number-title"
              >Номер карты</label
            >
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
              class="number-error"
            >
              * Ошибка валидации или поле пустое
            </div>

            <div class="small-inputs-container">
              <div class="input-handler">
                <input
                  type="text"
                  name="card-date"
                  class="card-date-input"
                  placeholder="Срок действия"
                  v-mask="mask"
                  v-model="cardDateInput"
                  required
                />
                <div
                  v-if="!isFutureDate(cardDateInput) && hidden"
                  class="validation-error"
                >
                  * Поле пустое, или истек срок действия карты
                </div>
              </div>
              <div class="input-handler">
                <input
                  type="password"
                  name="card-cvc"
                  class="card-cvc-input"
                  placeholder="CVC код"
                  v-mask="'###'"
                  v-model="cardCvcInput"
                  required
                />
                <div
                  v-if="emptyCVV(cardCvcInput) && hidden"
                  class="validation-error"
                >
                  * Поле пустое или заполнено не до конца
                </div>
              </div>
            </div>
            <p class="payment-description">
              Для привязки карты мы проведем платеж в размере 1.00 UAH, который
              будет возвращен в течении 30 минут.
            </p>
            <div
              :disabled="clicked"
              type="button"
              class="get-money-button"
              @click.prevent="cardValidation"
            >
              <span class="loader" v-show="toggleSpinner"></span
              ><span class="get-money-title">Получить деньги</span>
            </div>
          </form>
        </div>
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
          this.hidden = true;
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
$form-padding: 16px;
$paragraph-size: 16px;
$button-gradient: linear-gradient(
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
$breakpoint-tablet: 768px;
$breakpoint-small: 640px;

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
  position: relative;
}
.language {
  color: white;
  display: flex;
  align-items: center;
  position: absolute;
  right: 275px;
  bottom: 40px;
}
.horizontal-line {
  margin-right: 2px;
  margin-left: 2px;
}
.ru-language {
  font-family: $core-sans-thin;
  font-size: 16px;
  color: #587bc3;
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
  font-weight: 200;
}
.main-container {
  width: 100%;
  min-height: 100vh;
  display: flex;
  justify-content: center;
}
.card-container {
  width: 66.666666%;
  height: 530px;
  border: 1px solid #e4e4e4;
  border-radius: 4px;
  margin-top: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 130px;
}
.form-container {
  padding-right: $form-padding;
  padding-left: $form-padding;
  box-sizing: border-box;
  width: 400px;
  height: 330px;
}
.card-number-title {
  font-size: 18px;
  font-weight: bold;
  width: 100%;
  display: inline-block;
}
.card-number-input {
  width: 100%;
  border-radius: 4px;
  border: 1px solid #cccccc;
  height: 55px;
  font-size: $paragraph-size;
  padding-left: $form-padding;
  padding-right: $form-padding;
  margin-top: $form-indent;
  position: relative;
  box-sizing: border-box;
}
.card-type-img-container {
  position: relative;
}

.card-type-img {
  position: absolute;
  bottom: 15px;
  left: 315px;
  width: 42px;
  height: 28px;
}
.input-handler {
  width: 48%;
}
.card-date-input {
  width: 100%;
  border-radius: 4px;
  border: 1px solid #cccccc;
  height: 55px;
  font-size: $paragraph-size;
  padding-left: $form-padding;
  padding-right: $form-padding;
  margin-top: $form-indent;
  box-sizing: border-box;
}
.card-cvc-input {
  width: 100%;
  border-radius: 4px;
  border: 1px solid #cccccc;
  height: 55px;
  font-size: $paragraph-size;
  padding-left: $form-padding;
  padding-right: $form-padding;
  margin-top: $form-indent;
  box-sizing: border-box;

  &:focus {
  }
}

.small-inputs-container {
  display: flex;
  width: 100%;
  justify-content: space-between;
  height: 125px;
}
.payment-description {
  color: #808080;
  font-size: $paragraph-size;
  line-height: 20px;
  font-family: $core-sans-light;
  font-weight: 100;
}
.get-money-button {
  background: $button-gradient;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 56px;
  width: 100%;
  border-radius: 0;
  user-select: none;
  cursor: pointer;
}
.get-money-title {
  font-size: $paragraph-size;
  color: #ffffff;
  margin-left: 7px;
}
.number-error {
  color: #ff8d8d;
  font-size: 12px;
  line-height: 167%;
  margin-top: 10px;
  width: 60%;
}
.validation-error {
  color: #ff8d8d;
  font-size: 12px;
  line-height: 167%;
  margin-top: 10px;
  width: 100%;
}

.loader {
  border: 4px solid #f3f3f3; /* Light grey */
  border-top: 4px solid #3498db; /* Blue */
  border-radius: 50%;
  width: 10px;
  height: 10px;
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
@media (max-width: 1200px) {
  .language {
    right: 235px;
  }
}
@media (max-width: 1100px) {
  .language {
    right: 200px;
  }
}
@media (max-width: 1000px) {
  .language {
    right: 165px;
  }
}
@media (max-width: 900px) {
  .language {
    right: 130px;
  }
}
@media (max-width: 800px) {
  .language {
    right: 95px;
  }
}
@media (max-width: $breakpoint-tablet) {
  .header {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .logo-header {
    margin-left: 25px;
  }
  .language {
    position: static;
    margin-right: 25px;
  }
  .title {
    margin-top: 20px;
    width: 50%;
    margin-right: auto;
    margin-left: auto;
    height: auto;
    font-weight: 200;
    font-size: 25px;
    text-align: center;
    margin-bottom: 33px;
  }
  .card-number-title {
    display: none;
  }
}
@media (max-width: $breakpoint-small) {
  .card-container {
    width: 90%;
  }
}
</style>
