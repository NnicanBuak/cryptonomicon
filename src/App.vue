<template>
  <div class="container mx-auto flex flex-col items-center p-4">
    <div
      style="display: none"
      class="fixed w-100 h-100 opacity-80 bg-purple-800 inset-0 z-50 flex items-center justify-center"
    >
      <svg
        class="animate-spin -ml-1 mr-3 h-12 w-12 text-white"
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
      >
        <circle
          class="opacity-25"
          cx="12"
          cy="12"
          r="10"
          stroke="currentColor"
          stroke-width="4"
        ></circle>
        <path
          class="opacity-75"
          fill="currentColor"
          d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
        ></path>
      </svg>
    </div>
    <div class="container">
      <section>
        <div class="flex">
          <div class="max-w-xs">
            <label for="wallet" class="block text-sm font-medium text-gray-900"
              >Тикер</label
            >
            <div
              style="z-index: 1"
              class="p-1 mt-1 relative rounded-md shadow-md bg-purple-800"
            >
              <input
                v-model="searchTickerInput"
                v-on:keydown.enter="addTicker"
                type="text"
                name="wallet"
                id="wallet"
                class="block w-full pr-10 mb-1 border-gray-300 text-gray-900 focus:outline-none focus:ring-gray-500 focus:border-gray-500 sm:text-sm rounded"
                placeholder="Например DOGE"
              />
              <span
                @click="this.searchTickerInput = 'BTC'"
                class="inline-flex items-center px-2 m-1 rounded text-xs font-medium bg-purple-600 text-white cursor-pointer hover:bg-purple-500"
              >
                BTC
              </span>
              <span
                @click="this.searchTickerInput = 'DOGE'"
                class="inline-flex items-center px-2 m-1 rounded text-xs font-medium bg-purple-600 text-white cursor-pointer hover:bg-purple-500"
              >
                DOGE
              </span>
              <span
                @click="this.searchTickerInput = 'BCH'"
                class="inline-flex items-center px-2 m-1 rounded text-xs font-medium bg-purple-600 text-white cursor-pointer hover:bg-purple-500"
              >
                BCH
              </span>
              <span
                @click="this.searchTickerInput = 'CHD'"
                class="inline-flex items-center px-2 m-1 rounded text-xs font-medium bg-purple-600 text-white cursor-pointer hover:bg-purple-500"
              >
                CHD
              </span>
            </div>
            <transition name="slide">
              <div
                v-if="tickerInputIsAlreadyInUse"
                style="
                  width: fit-content;
                  border-radius: 0 0 0.375rem 0.375rem;
                  justify-content: center;
                "
                class="bg-red-500 flex ml-1 p-2 rounded-md flex-wrap text-sm text-white"
              >
                Такой тикер уже добавлен
              </div>
            </transition>
          </div>
        </div>
        <button
          @click="addTicker"
          type="button"
          class="my-4 inline-flex items-center py-2 px-4 border border-transparent shadow-sm text-sm leading-4 font-medium rounded-full text-white bg-green-700 hover:bg-green-600 transition-colors duration-300 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500"
        >
          <!-- Heroicon name: solid/mail -->
          <svg
            class="-ml-0.5 mr-2 h-6 w-6"
            xmlns="http://www.w3.org/2000/svg"
            width="30"
            height="30"
            viewBox="0 0 24 24"
            fill="#ffffff"
          >
            <path
              d="M13 7h-2v4H7v2h4v4h2v-4h4v-2h-4V7zm-1-5C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8z"
            ></path>
          </svg>
          Добавить
        </button>
      </section>
      <template v-if="tickers.length > 0">
        <hr class="w-full border-t border-gray-600 bg-gray-100 pb-8" />
        <dl class="mb-8 grid grid-cols-1 gap-5 bg-gray-100 sm:grid-cols-3">
          <div
            v-for="t in tickers"
            :key="t"
            @click="selectedTicker = t"
            :class="{ 'border-4': selectedTicker === t }"
            class="flex flex-col justify-between bg-white overflow-hidden shadow rounded-lg border-purple-800 border-solid cursor-pointer"
          >
            <div class="px-4 py-5 sm:p-6 text-center">
              <dt class="text-sm font-medium text-gray-500 truncate">
                {{ t.name }} - USD
              </dt>
              <dd class="mt-1 text-3xl font-semibold text-gray-900">
                {{ t.value }}
              </dd>
            </div>
            <div class="w-full border-t border-gray-200">
              <button
                @click.stop="removeTicker(t)"
                class="flex items-center justify-center font-medium w-full bg-gray-100 px-4 py-4 sm:px-6 text-md text-gray-500 hover:text-gray-600 hover:bg-gray-200 hover:opacity-20 transition-all focus:outline-none"
              >
                <svg
                  class="h-5 w-5"
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 20 20"
                  fill="#718096"
                  aria-hidden="true"
                >
                  <path
                    fill-rule="evenodd"
                    d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z"
                    clip-rule="evenodd"
                  ></path></svg
                >Удалить
              </button>
            </div>
          </div>
        </dl>
        <hr class="w-full border-t border-gray-600 pt-8" />
      </template>
      <section v-for="g in priceGraphs" :key="g" class="relative">
        <div v-show="selectedTicker.name === g.name">
          <h3 class="text-lg leading-6 font-medium text-gray-900 my-8">
            {{ g.name }} - USD
          </h3>
          <div class="flex items-end border-gray-600 border-b border-l h-64">
            <div
              v-for="(bar, index) in normalizeGraph(g.prices)"
              :key="index"
              :style="{ height: `${bar}% ` }"
              class="bg-purple-800 border w-10"
            ></div>
          </div>
          <button
            @click="selectedTicker = null"
            type="button"
            class="absolute top-0 right-0"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              xmlns:xlink="http://www.w3.org/1999/xlink"
              xmlns:svgjs="http://svgjs.com/svgjs"
              version="1.1"
              width="30"
              height="30"
              x="0"
              y="0"
              viewBox="0 0 511.76 511.76"
              style="enable-background: new 0 0 512 512"
              xml:space="preserve"
            >
              <g>
                <path
                  d="M436.896,74.869c-99.84-99.819-262.208-99.819-362.048,0c-99.797,99.819-99.797,262.229,0,362.048    c49.92,49.899,115.477,74.837,181.035,74.837s131.093-24.939,181.013-74.837C536.715,337.099,536.715,174.688,436.896,74.869z     M361.461,331.317c8.341,8.341,8.341,21.824,0,30.165c-4.16,4.16-9.621,6.251-15.083,6.251c-5.461,0-10.923-2.091-15.083-6.251    l-75.413-75.435l-75.392,75.413c-4.181,4.16-9.643,6.251-15.083,6.251c-5.461,0-10.923-2.091-15.083-6.251    c-8.341-8.341-8.341-21.845,0-30.165l75.392-75.413l-75.413-75.413c-8.341-8.341-8.341-21.845,0-30.165    c8.32-8.341,21.824-8.341,30.165,0l75.413,75.413l75.413-75.413c8.341-8.341,21.824-8.341,30.165,0    c8.341,8.32,8.341,21.824,0,30.165l-75.413,75.413L361.461,331.317z"
                  fill="#718096"
                  data-original="#000000"
                ></path>
              </g>
            </svg>
          </button>
        </div>
      </section>
    </div>
  </div>
</template>

<script>
// import tickerList from "./components/ticker-list.vue";
export default {
  name: "App",
  // components: { tickerList },

  data() {
    return {
      searchTickerInput: "",
      tickers: [],
      selectedTicker: null,
      tickerInputIsAlreadyInUse: false,
      priceGraphs: [], // { name: ticker.name, prices: [] }
    };
  },

  watch: {
    searchTickerInput(newValue) {
      this.searchTickerInput = newValue.toUpperCase().trim();
      this.tickerInputIsAlreadyInUse = this.tickers.some(
        (t) => t.name === newValue
      )
        ? true
        : false;
    },
    tickers(newValue) {
      this.tickerInputIsAlreadyInUse = newValue.some(
        (t) => t.name === this.searchTickerInput
      )
        ? true
        : false;
    },
  },
  methods: {
    addTicker() {
      const ticker = {
        name: this.searchTickerInput,
        value: "-",
      };
      if (this.tickerInputIsAlreadyInUse) {
        // Ещё не придумал как отсылаться к DOM элементу
      } else if (this.searchTickerInput !== "") {
        this.tickers.push(ticker);
        this.selectedTicker = ticker;
        this.priceGraphs.push({ name: ticker.name, prices: [] });

        const fetchData = async () => {
          const f = await fetch(
            `https://min-api.cryptocompare.com/data/price?fsym=${ticker.name}&tsyms=USD&api_key=d646375d1bdb55ba2a2c3fda4b9058b1426d863b0e303a373060891421744b63`
          );

          const data = await f.json();
          const price =
            data.USD > 1 ? data.USD.toFixed(2) : data.USD.toPrecision(2);
          console.log(this.tickers.find((t) => t.name === ticker.name).value);
          this.tickers.find((t) => t.name === ticker.name).value = price;
          if (this.selectedTicker?.name === ticker.name) {
            this.priceGraphs
              .find((g) => g.name === ticker.name)
              .prices.push(price);
          }
        };
        fetchData();

        setInterval(fetchData(), 10000);
        this.searchTickerInput = "";
      }
    },
    removeTicker(ticker) {
      this.tickers = this.tickers.filter((t) => t !== ticker);
      if (ticker === this.selectedTicker) this.selectedTicker = null;
      this.priceGraphs = this.priceGraphs.filter((g) => g[0] !== ticker.name);
    },
    normalizeGraph(prices) {
      const maxValue = Math.max(...prices);
      const minValue = Math.min(...prices);
      return prices.map((price) => {
        5 + ((price - minValue) * 95) / (maxValue - minValue);
      });
    },
  },
};
</script>

<style src="./tailwind.css"></style>
<style src="./general.css"></style>
