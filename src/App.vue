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
            type="number"
            name="card-number"
            class="card-number-input"
            placeholder="Номер карты"
          />
          <div class="small-inputs-container">
            <input
              type="number"
              name="card-date"
              class="card-date-input"
              placeholder="Срок действия"
            />
            <input
              type="number"
              name="card-cvc"
              class="card-cvc-input"
              placeholder="CVC код"
            />
          </div>
          <p class="payment-description">
            Для привязки карты мы проведем платеж в размере 1.00 UAH, который
            будет возвращен в течении 30 минут.
          </p>
          <div class="get-money-button">
            <span class="get-money-title">Получить деньги</span>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {},
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
$button-gradient: linear-gradient(
  122.5deg,
  #5a4be6 -33.07%,
  #73aff7 48.35%,
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
  height: 430px;
  border: 1px solid #e4e4e4;
  border-radius: 4px;
  margin-left: auto;
  margin-right: auto;
  margin-top: 20px;
  display: flex;
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
  background: $button-gradient;
  display: flex;
  height: 56px;
  width: 100%;
}
.get-money-title {
  margin: auto;
  font-size: $paragraph-size;
  color: #ffffff;
}
</style>