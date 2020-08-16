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
              :class="{
                'card-number-input': true,
                errorBorder: errorInputNumberBorder,
              }"
              placeholder="Номер карты"
              v-mask="'#### #### #### ####'"
              v-model="cardNumberInput"
            />
            <div class="card-type-img-container">
              <!-- Динамическая вставка изображения брэнда карты -->
              <img class="card-type-img" :src="imgURL" :alt="imgAlt" />
            </div>
            <!-- Сообщение об ошибке (после нажатия кнопки) -->
            <div
              v-if="!validCreditCard(cardNumberInput) && hidden"
              class="number-error"
            >
              * Ошибка валидации, поле пустое или заполнено не до конца
            </div>

            <div class="small-inputs-container">
              <div class="input-handler">
                <input
                  type="text"
                  name="card-date"
                  :class="{
                    'card-date-cvc-input': true,
                    errorBorder: errorInputDateBorder,
                  }"
                  placeholder="Срок действия"
                  v-mask="mask"
                  v-model="cardDateInput"
                />
                <!-- Сообщение об ошибке (после нажатия кнопки) -->
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
                  name="card-date-cvc-input"
                  :class="{
                    'card-date-cvc-input': true,
                    errorBorder: errorInputCVCBorder,
                  }"
                  placeholder="CVC код"
                  v-mask="'###'"
                  v-model="cardCvcInput"
                />
                <!-- Сообщение об ошибке (после нажатия кнопки) -->
                <div
                  v-if="isEmptyCVC(cardCvcInput) && hidden"
                  class="validation-error"
                >
                  * Поле пустое или заполнено не до конца
                </div>
              </div>
            </div>
            <p class="payment-description">
              Для привязки карты мы проведем платеж в размере 1.00 UAH, который
              будет возвращен в течение 30 минут.
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
  </div>
</template>

<script>
export function cardExpirationMask(value) {
  /* Маска для поля ввода даты карты */
  const month = [
    /[0-1]/,
    value.charAt(0) === '1'
      ? /[0-2]/
      : /[0-9]/ || value.charAt(0) === '0'
      ? /[1-9]/
      : /[0-9]/,
  ];
  const year = [/[0-9]/, value.charAt(3) === '0' ? /[1-9]/ : /[0-9]/];
  return value.length > 2 ? [...month, '/', ...year] : month;
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
      /* Проверка брэнда карты */
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
    validCreditCard(value) {
      /* Проверка карты на валидацию по алгоритму Луна */
      if (/[^0-9-\s]+/.test(value)) return false;
      if (!value) return false;

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
      /* Проверка даты на действительность */
      let today = new Date().getTime();
      idate = idate.split('/');

      idate = new Date('20' + idate[1], idate[0] - 1, 0).getTime();
      return today - idate < 0;
    },

    isEmptyNumber(value) {
      /* Проверка на полное введение номера карты*/
      if (this.validCreditCard(this.cardNumberInput) === true) {
        return false;
      } else if (value.length < 18) {
        return true;
      } else {
        return false;
      }
    },
    isEmptyDate(value) {
      /* Проверка на полное введение даты карты */
      if (value.length < 5) {
        return true;
      } else {
        return false;
      }
    },
    isEmptyCVC(value) {
      /* Проверка на полное введение CVC карты */
      if (value.length < 3) {
        return true;
      } else {
        return false;
      }
    },

    cardValidation() {
      /* Проверка карты на валидность */
      this.clicked = true;
      this.toggleSpinner = true;
      if (
        this.validCreditCard(this.cardNumberInput) === true &&
        this.isFutureDate(this.cardDateInput) === true &&
        this.isEmptyCVC(this.cardCvcInput) === false &&
        this.isEmptyNumber(this.cardNumberInput) === false &&
        this.isEmptyDate(this.cardDateInput) === false &&
        this.isEmptyCVC(this.cardCvcInput) === false
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
      /* Генерация пути для изображения брэнда карты */
      if (!this.creditCardType(this.cardNumberInput)) {
        return require(`@/assets/credit-card-brands/credit-cards.svg`);
      }
      let typeName = this.creditCardType(this.cardNumberInput);
      return require(`@/assets/credit-card-brands/${typeName}.svg`);
    },
    imgAlt() {
      /* Генерация alt-a брэнда карты */
      return this.creditCardType(this.cardNumberInput);
    },
    errorInputNumberBorder() {
      /* Отображение красного бордера, если номер введен не полностью (после нажатия на кнопку) */
      if (
        this.hidden === true &&
        this.isEmptyNumber(this.cardNumberInput) === true
      ) {
        return true;
      } else {
        return false;
      }
    },
    errorInputDateBorder() {
      /* Отображение красного бордера, если дата введена не полностью (после нажатия на кнопку) */
      if (
        this.hidden === true &&
        this.isEmptyDate(this.cardDateInput) === true
      ) {
        return true;
      } else {
        return false;
      }
    },
    errorInputCVCBorder() {
      /* Отображение красного бордера, если cvc введен не полностью (после нажатия на кнопку) */
      if (this.hidden === true && this.isEmptyCVC(this.cardCvcInput) === true) {
        return true;
      } else {
        return false;
      }
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
$full: 100%;
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
  width: $full;
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
  margin-right: 4px;
  margin-left: 4px;
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
  width: $full;
  display: flex;
  justify-content: center;
}
.card-container {
  width: 66.666666%;
  height: $full;
  border: 1px solid #e4e4e4;
  border-radius: 4px;
  margin-top: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 130px;
  padding-bottom: 50px;
  padding-top: 50px;
}
.form-container {
  padding-right: $form-padding;
  padding-left: $form-padding;
  box-sizing: border-box;
  width: 400px;
  height: $full;
}
.card-number-title {
  font-size: 18px;
  font-weight: bold;
  width: $full;
  display: inline-block;
}
.card-number-input {
  width: $full;
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
.card-date-cvc-input {
  width: $full;
  border-radius: 4px;
  border: 1px solid #cccccc;
  height: 55px;
  font-size: $paragraph-size;
  padding-left: $form-padding;
  padding-right: $form-padding;
  margin-top: $form-indent;
  box-sizing: border-box;
}
.errorBorder {
  border: 1px solid #ff8d8d;
}

.small-inputs-container {
  display: flex;
  width: $full;
  justify-content: space-between;
  height: auto;
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
  width: $full;
  border-radius: 0;
  user-select: none;
  cursor: pointer;
  border-radius: 4px;
  border: 0 solid black;
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
  width: $full;
}
.validation-error {
  color: #ff8d8d;
  font-size: 12px;
  line-height: 167%;
  margin-top: 10px;
  width: $full;
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
@media (max-width: 435px) {
  .card-type-img {
    left: 305px;
  }
}
@media (max-width: 425px) {
  .card-type-img {
    left: 295px;
  }
}
@media (max-width: 415px) {
  .card-type-img {
    left: 285px;
  }
}
@media (max-width: 405px) {
  .card-type-img {
    left: 275px;
  }
}
@media (max-width: 395px) {
  .card-type-img {
    left: 265px;
  }
}
@media (max-width: 385px) {
  .card-type-img {
    left: 255px;
  }
}
@media (max-width: 375px) {
  .card-type-img {
    left: 245px;
  }
}
@media (max-width: 365px) {
  .card-type-img {
    left: 235px;
  }
}

@media (max-width: $breakpoint-small) {
  .card-container {
    width: 90%;
    padding-top: $form-padding;
    padding-bottom: $form-padding;
  }
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
    width: 60%;
    margin-right: auto;
    margin-left: auto;
    height: auto;
    font-weight: 200;
    font-size: 25px;
    text-align: center;
    margin-bottom: 17px;
  }
  .card-number-title {
    display: none;
  }
  .card-number-input {
    margin-top: 0;
  }
  .get-money-button {
    margin-top: 32px;
  }
}
</style>
