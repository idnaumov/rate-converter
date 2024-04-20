<script>
  import axios from 'axios';
  import { currencyList } from './data/currencyList';

  let currenciesData = []; // инициализируем массив с будущими данными для конвертации
  let lastUpdateTime = 0; 
  let nextUpdateTime = 86400; // получаем следующие сутки для заглушки

  let selectedCurrency = 0; // выбранная валюта

  let activeIndexMe = 0; 
  let activeIndexGet = 0;
  async function clickOnCurrency(index, type) {
    if (type === 'me') {
      activeIndexMe = index;
      await getCurrenciesData();
      changeSum(1);
    } else {
      activeIndexGet = index;
      changeSum(2);
    }
  }

  async function getCurrenciesData() {
    await axios
    .get('https://v6.exchangerate-api.com/v6/0b349f61e9f230406b9f4c68/latest/' + currencyList[activeIndexMe])
    .then(response => {
      currenciesData = response.data.conversion_rates;
      lastUpdateTime = response.data.time_last_update_unix;
      nextUpdateTime = response.data.time_next_update_unix;
    });
  } 
  // Вызываем функцию сразу после инициализации для получения актуальных данных
  getCurrenciesData()

  let sumOnMe = 1.00
  let sumToGet = 1.00
  // Конвертация валюты
  function changeSum(type) {
    const currentCurrency = currencyList[activeIndexGet];
    const exchangeRate = currenciesData[currentCurrency];

    if (type === 1) {
        sumToGet = sumOnMe * exchangeRate;
        sumToGet = parseFloat(sumToGet.toFixed(2))
    } else if (type === 2) {
        sumOnMe = sumToGet / exchangeRate;
        sumOnMe = parseFloat(sumOnMe.toFixed(2))
    }
}

  function formatUnixTime(unix) {
    // Преобразуем UNIX в миллисекунды
    const date = new Date(unix * 1000);

    const day = date.getDate() < 10 ? '0' + date.getDate() : date.getDate();
    const month = date.getMonth() + 1 < 10 ? '0' + ( date.getMonth() + 1 ) : date.getMonth() + 1;
    const year = date.getFullYear() < 10 ? '0' + date.getFullYear() : date.getFullYear();
    const hours = date.getHours() < 10 ? '0' + date.getHours() : date.getHours();
    const minutes = date.getMinutes() < 10 ? '0' + date.getMinutes() : date.getMinutes();

    return `${day}.${month}.${year} ${hours}:${minutes}`
  }
</script>

<main>

  <h1>Конвертер валют онлайн</h1>
  <div class="converter">
    <div class="currency-block">
        <span class="currency-block__title">У меня есть</span>
        <ul class="currency-panel">
          {#each currencyList.slice(0, 3) as currency, index}
            <li
              class="currency-panel__item {activeIndexMe === index ? 'active' : ''}"
              on:click={() => clickOnCurrency(index, 'me')}
            >
              {currency}
            </li>
          {/each}
          <li class="currency-panel__item" on:click={() => { selectedCurrency === 0 ? selectedCurrency = 1 : selectedCurrency = 0 }}>
            <svg width="18.000000" height="17.000000" viewBox="0 0 34 17" fill="none" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
              <defs>
                <clipPath id="clip141_17">
                  <rect id="svg" width="34.000000" height="17.000000" fill="white" fill-opacity="0"/>
                </clipPath>
              </defs>
              <g clip-path="url(#clip141_17)">
                <path id="path" d="M17 16.66C16.47 16.66 15.95 16.48 15.54 16.13L1.94 4.68C1.48 4.29 1.19 3.73 1.14 3.12C1.08 2.52 1.27 1.91 1.65 1.45C2.04 0.98 2.59 0.68 3.19 0.63C3.79 0.57 4.38 0.76 4.85 1.15L17 11.41L29.15 1.51C29.38 1.32 29.64 1.18 29.93 1.1C30.22 1.01 30.52 0.98 30.81 1.01C31.11 1.05 31.4 1.14 31.66 1.28C31.92 1.43 32.15 1.62 32.34 1.86C32.55 2.09 32.71 2.37 32.8 2.67C32.9 2.97 32.93 3.29 32.9 3.6C32.87 3.91 32.78 4.22 32.63 4.5C32.48 4.77 32.27 5.01 32.02 5.2L18.42 16.27C18 16.56 17.5 16.7 17 16.66Z" fill="#FFFFFF" fill-opacity="1.000000" fill-rule="nonzero" class="arrow-down"/>
              </g>
            </svg>            
          </li>
          <ul class="currency-list {selectedCurrency !== 1 ? 'hide' : ''}">
            {#each currencyList as currency, index}
            <li class="currency-list__item {activeIndexMe === index ? 'active' : ''}" on:click="{() => { clickOnCurrency(index, 'me'); selectedCurrency = 0; }}">
              {currency}
            </li>
            {/each}
          </ul>
        </ul>
        <input type="number" class="currency-block__input" id="toMe" placeholder="0.00" bind:value={sumOnMe} on:input={() => changeSum(1)}>
        <label for="toMe" class="currency-block__label">{currencyList[activeIndexMe]}</label>
    </div>

    <div class="currency-block">
      <span class="currency-block__title">Я получу</span>
      <ul class="currency-panel">
        {#each currencyList.slice(0, 3) as currency, index}
            <li
              class="currency-panel__item {activeIndexGet === index ? 'active' : ''}"
              on:click={() => clickOnCurrency(index, 'get')}
            >
              {currency}
            </li>
          {/each}
          <li class="currency-panel__item" on:click={() => { selectedCurrency === 0 ? selectedCurrency = 2 : selectedCurrency = 0 }}>
            <svg width="18.000000" height="17.000000" viewBox="0 0 34 17" fill="none" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
              <defs>
                <clipPath id="clip141_17">
                  <rect id="svg" width="34.000000" height="17.000000" fill="white" fill-opacity="0"/>
                </clipPath>
              </defs>
              <g clip-path="url(#clip141_17)">
                <path id="path" d="M17 16.66C16.47 16.66 15.95 16.48 15.54 16.13L1.94 4.68C1.48 4.29 1.19 3.73 1.14 3.12C1.08 2.52 1.27 1.91 1.65 1.45C2.04 0.98 2.59 0.68 3.19 0.63C3.79 0.57 4.38 0.76 4.85 1.15L17 11.41L29.15 1.51C29.38 1.32 29.64 1.18 29.93 1.1C30.22 1.01 30.52 0.98 30.81 1.01C31.11 1.05 31.4 1.14 31.66 1.28C31.92 1.43 32.15 1.62 32.34 1.86C32.55 2.09 32.71 2.37 32.8 2.67C32.9 2.97 32.93 3.29 32.9 3.6C32.87 3.91 32.78 4.22 32.63 4.5C32.48 4.77 32.27 5.01 32.02 5.2L18.42 16.27C18 16.56 17.5 16.7 17 16.66Z" fill="#FFFFFF" fill-opacity="1.000000" fill-rule="nonzero" class="arrow-down"/>
              </g>
            </svg>            
          </li>
          <ul class="currency-list {selectedCurrency !== 2 ? 'hide' : ''}">
            {#each currencyList as currency, index}
            <li class="currency-list__item {activeIndexGet === index ? 'active' : ''}" on:click="{() => { clickOnCurrency(index, 'get'); selectedCurrency = 0; }}">
              {currency}
            </li>
            {/each}
          </ul>
      </ul>
      <input type="number" class="currency-block__input" id="toGet" placeholder="0.00" bind:value={sumToGet} on:input={() => changeSum(2)}>
      <label for="toGet" class="currency-block__label">{currencyList[activeIndexGet]}</label>
    </div>
  </div>
  
  <span class="main__update-text">Последнее обновление данных: {formatUnixTime(lastUpdateTime)}</span>
  <span class="main__update-text small">Следующее обновление данных: {formatUnixTime(nextUpdateTime)}</span>
  <a href="https://www.exchangerate-api.com" class="main__rate-link">Rates By Exchange Rate API</a>
</main>

<style>
@import url('style/main.scss');
</style>
