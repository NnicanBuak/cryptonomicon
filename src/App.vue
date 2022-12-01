<template>
  <div class="container mx-auto flex flex-col items-center p-4">
    <transition name="fade">
      <div
        v-if="!contentIsLoaded"
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
        </svg></div
    ></transition>
    <div class="container">
      <section>
        <div class="flex">
          <div @mouseleave="searchHintsShowed = false" class="max-w-full">
            <label for="wallet" class="block text-sm font-medium text-gray-900"
              >Тикер</label
            >
            <div
              style="z-index: 1"
              class="p-1 mt-1 relative rounded-md shadow-md bg-gray-300"
            >
              <input
                v-model="searchTickerInput"
                @keydown.enter="addTicker(searchTickerInput)"
                @mouseenter="searchHintsShowed = true"
                @focus="searchHintsShowed = true"
                type="text"
                name="wallet"
                id="wallet"
                placeholder="Например DOGE"
                class="input-danger w-full block pr-10 border-gray-300 text-gray-900 focus:outline-none focus:ring-gray-500 focus:border-gray-500 sm:text-sm rounded"
              />
              <svg
                v-show="searchTickerInputIsAlreadyInUse"
                width="528px"
                height="528px"
                viewBox="0 -8 528 528"
                xmlns="http://www.w3.org/2000/svg"
                style="
                  width: 1.25rem;
                  height: 1.25rem;
                  transform: translate3d(-50%, 65%, 0);
                  filter: invert(60%) sepia(77%) saturate(2671%)
                    hue-rotate(321deg) brightness(101%) contrast(92%);
                "
                class="absolute top-0 right-0"
              >
                <title>Такой тикер уже добавлен</title>
                <path
                  d="M264 56Q318 56 364 83 410 110 437 156 464 202 464 256 464 310 437 356 410 402 364 429 318 456 264 456 210 456 164 429 118 402 91 356 64 310 64 256 64 202 91 156 118 110 164 83 210 56 264 56ZM232 144L232 272 296 272 296 144 232 144ZM232 304L232 368 296 368 296 304 232 304Z"
                />
              </svg>
            </div>
            <transition name="slide">
              <div
                v-if="searchHints.length > 0"
                v-show="searchTickerInput !== '' || searchHintsShowed"
                style="
                  width: fit-content;
                  border-radius: 0 0 0.375rem 0.375rem;
                  justify-content: center;
                  margin-top: -0.2rem;
                "
                class="w-auto mx-2 p-1 rounded-md shadow-md bg-purple-300"
              >
                <span
                  v-for="h in searchHints"
                  :key="h"
                  @click="(this.searchTickerInput = h), addTicker(h)"
                  class="inline-flex items-center px-2 m-1 rounded text-xs font-medium bg-purple-500 text-white cursor-pointer hover:bg-purple-600"
                >
                  {{ h }}
                </span>
              </div>
            </transition>
          </div>
        </div>
        <button
          @click="addTicker(searchTickerInput)"
          type="button"
          class="my-4 inline-flex items-center py-2 px-4 border border-transparent shadow-sm text-sm leading-4 font-medium rounded-full text-white bg-green-400 hover:bg-green-500 transition-colors duration-300 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500"
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
        <dl class="pb-8 grid grid-cols-1 gap-5 bg-gray-100 sm:grid-cols-3">
          <div
            v-for="t in tickers"
            :key="t.name"
            @click="selectTicker(t)"
            :class="{
              'border-4': selectedTickerName === t.name,
            }"
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
      <section v-show="selectedTickerName !== null" class="relative">
        <h3 class="text-lg leading-6 font-medium text-gray-900 mt-8">
          {{ this.selectedTickerName }} - USD
        </h3>
        <h3 class="bold text-lg leading-3 font-light text-gray-900 mb-8">
          {{ this.graph[0]?.value }}
        </h3>
        <div
          @mousewheel="scaleGraph"
          :class="{ 'graph-scaled': this.graphIsScaling }"
          class="overflow-hidden flex flex-row-reverse border-gray-600 border-b border-l h-64"
        >
          <div
            v-for="(bar, index) in normalizeGraph(this.graph)"
            :key="index"
            class="h-full flex items-end"
          >
            <div
              :title="`${bar.price}$\n${bar.time}`"
              :style="{
                height: `${bar.height}% `,
                width: `${this.graphScale}rem`,
              }"
              class="bg-purple-800 border w-16"
            ></div>
          </div>
        </div>
        <button
          @click="selectTicker()"
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
      contentIsLoaded: false,
      coinList: [],
      fetchDataUpdateTime: 60000,
      searchTickerInput: "",
      searchTickerInputIsAlreadyInUse: false,
      searchHintsShowed: false,
      searchHints: ["BTC", "ETH", "LTC", "BCH"],
      searchHintsDefault: [
        { name: "BTC", show: true },
        { name: "ETH", show: true },
        { name: "LTC", show: true },
        { name: "BCH", show: true },
      ],
      tickers: [], // { name: "", value: [] }
      selectedTickerName: null, // ""
      graph: [],
      graphScale: 2.25,
      graphIsScaling: false,
    };
  },

  created() {
    (async () => {
      const promise = await fetch(
        "https://min-api.cryptocompare.com/data/all/coinlist?summary=true"
      );
      const data = await promise.json();
      this.coinList = Object.keys(data.Data);
    })();

    const tickersData = localStorage.getItem("cryptonomicon-list");
    if (tickersData) {
      this.tickers = JSON.parse(tickersData);
      this.tickers.forEach((t) => this.subscribeToFetchData(t.name));
    }

    this.searchHintsDefault
      .filter((h) => this.tickers.some((t) => t.name === h.name))
      .forEach((h) => (h.show = false));
  },

  mounted() {
    this.contentIsLoaded = true;
  },

  watch: {
    searchHintsDefault: {
      handler(newValue) {
        // Note: `newValue` will be equal to `oldValue` here
        // on nested mutations as long as the object itself
        // hasn't been replaced.
        if (this.searchTickerInput === "") {
          this.searchHints = newValue.filter((h) => h.show).map((h) => h.name);
        }
      },
      deep: true,
    },

    searchTickerInput(newValue) {
      this.searchTickerInput = newValue.toUpperCase().trim();
      this.searchTickerInputIsAlreadyInUse = this.tickers.some(
        (t) => t.name === newValue
      )
        ? true
        : false;
      let searchHints;
      if (newValue !== "") {
        searchHints = this.coinList
          .filter(
            (c) =>
              c.slice(0, this.searchTickerInput.length) ===
                this.searchTickerInput &&
              (this.tickers.every((t) => t.name !== c) || this.tickers === [])
          )
          .sort();
      } else
        searchHints = this.searchHintsDefault
          .filter((h) => h.show)
          .map((h) => h.name);

      this.searchHints = searchHints.slice(0, 4);
    },

    tickers(newValue) {
      this.searchTickerInputIsAlreadyInUse = newValue.some(
        (t) => t.name === this.searchTickerInput
      )
        ? true
        : false;
    },
  },
  methods: {
    subscribeToFetchData(tickerName) {
      setInterval(async () => {
        const promise = await fetch(
          `https://min-api.cryptocompare.com/data/price?fsym=${tickerName}&tsyms=USD&api_key=d646375d1bdb55ba2a2c3fda4b9058b1426d863b0e303a373060891421744b63`
        );

        const data = await promise.json(),
          price = data.USD > 1 ? data.USD.toFixed(2) : data.USD.toPrecision(2);
        const time = new Date();
        this.tickers.find((t) => t.name === tickerName).value = price;
        if (this.selectedTickerName === tickerName) {
          this.graph.unshift({
            time: time.toTimeString().slice(0, 5),
            value: price,
          });
        }
      }, this.fetchDataUpdateTime);
    },
    addTicker(name) {
      if (
        this.searchHintsDefault
          .map((h) => h.name)
          .some((h) => this.searchHints.includes(h))
      )
        this.searchHintsDefault.find((h) => h.name === name).show = false;

      if (
        name !== "" &&
        !this.searchTickerInputIsAlreadyInUse &&
        this.coinList.includes(this.searchTickerInput)
      ) {
        const ticker = {
          name: name,
          value: "-",
        };
        this.tickers.unshift(ticker);

        localStorage.setItem(
          "cryptonomicon-list",
          JSON.stringify(this.tickers)
        );
        this.subscribeToFetchData(ticker.name);
        this.selectedTickerName = ticker.name;
        this.graph = [];
        this.searchTickerInput = "";
      }
    },

    selectTicker(ticker = null) {
      if (ticker !== null && ticker?.name !== this.selectedTickerName) {
        this.selectedTickerName = ticker.name;
        if (ticker.value !== "-") {
          const time = new Date();
          this.graph = [
            {
              time: `${time.toTimeString().slice(0, 5)}?`,
              value: ticker.value,
            },
          ];
        }
      } else {
        this.selectedTickerName = null;
        this.graph = [];
      }
      this.graphScale = 2.25;
    },

    removeTicker(ticker) {
      this.tickers = this.tickers.filter((t) => t !== ticker);
      this.selectedTickerName = null;
      this.graph = [];
      this.searchHintsDefault.find((h) => h.name === ticker.name).show = true;
      localStorage.removeItem(
        "cryptonomicon-list",
        JSON.stringify(this.tickers)
      );
    },

    normalizeGraph(graph) {
      let prices = [];
      for (const p of graph) prices.push(p.value);
      const maxValue = Math.max.apply(null, prices);
      const minValue = Math.min.apply(null, prices);

      return graph.map((price) => {
        if (maxValue !== minValue)
          return {
            height: 5 + ((price.value - minValue) * 95) / (maxValue - minValue),
            price: price.value,
            time: price.time,
          };
        else
          return {
            height: 50,
            price: price.value,
            time: price.time,
          };
      });
    },

    scaleGraph(event) {
      this.graphIsScaling = true;

      setTimeout(() => {
        this.graphIsScaling = false;
      }, 300);

      event.preventDefault();
      if (this.graphScale < 4 && event.deltaY === -125) {
        this.graphScale = this.graphScale + 0.25;
      }
      if (this.graphScale > 0.75 && event.deltaY === 125) {
        this.graphScale = this.graphScale - 0.25;
      }
    },
  },
};
</script>

<style>
.slide-enter-active,
.slide-leave-active {
  transition: transform 250ms ease;
}

.slide-enter-from,
.slide-leave-to {
  transform: translateY(-100%);
  box-shadow: 0px 0px 0px solid black;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 500ms ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.input-danger::after {
  content: "!";
}

.graph-scaled {
  cursor: w-resize;
}

.shake {
  animation: 0.7s shake;
}

@keyframes shake {
  10%,
  100% {
    transform: translate3d(-1px, 0, 0);
  }

  25%,
  85% {
    transform: translate3d(2px, 0, 0);
  }

  40%,
  70% {
    transform: translate3d(-3px, 0, 0);
  }

  55% {
    transform: translate3d(3px, 0, 0);
  }
}
</style>
<style src="./tailwind.css"></style>
