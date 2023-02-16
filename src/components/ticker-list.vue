<template v-if="tickers.length > 0">
  <section>
    <hr class="w-full border-t border-gray-600 bg-gray-100 pb-8" />
    <div :class="{ 'pb-8': this.tickers.length <= 6 }" class="bg-gray-100 px-4">
      <div class="filter flex content-center">
        <div
          style="border-radius: 0.375rem 0 0 0.375rem"
          class="relative p-2 rounded-md shadow-md bg-gray-200"
        >
          <svg
            class="w-4 h-4"
            version="1.1"
            id="Capa_1"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            x="0px"
            y="0px"
            viewBox="0 0 487.95 487.95"
            style="enable-background: new 0 0 487.95 487.95"
            xml:space="preserve"
          >
            <g>
              <g>
                <path
                  d="M481.8,453l-140-140.1c27.6-33.1,44.2-75.4,44.2-121.6C386,85.9,299.5,0.2,193.1,0.2S0,86,0,191.4s86.5,191.1,192.9,191.1,c45.2,0,86.8-15.5,119.8-41.4l140.5,140.5c8.2,8.2,20.4,8.2,28.6,0C490,473.4,490,461.2,481.8,453z M41,191.4,c0-82.8,68.2-150.1,151.9-150.1s151.9,67.3,151.9,150.1s-68.2,150.1-151.9,150.1S41,274.1,41,191.4z"
                />
              </g>
            </g>
          </svg>
        </div>
        <input
          v-model="tickersFilter"
          style="border-radius: 0 0.375rem 0.375rem 0"
          class="h-8 border-l-2 border-gray-300 rounded-md shadow-md bg-gray-300"
          type="text"
        />
      </div>

      <div v-if="this.tickers.length > 6" class="pagination flex p-2 gap-2">
        <button
          :disabled="this.tickersPage <= 1"
          @click="this.tickersPage = this.tickersPage - 1"
          class="border border-transparent focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500"
        >
          <svg
            class="w-6 h-6"
            version="1.1"
            id="Capa_1"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            x="0px"
            y="0px"
            viewBox="0 0 384.97 384.97"
            style="enable-background: new 0 0 384.97 384.97"
            xml:space="preserve"
          >
            <g>
              <g id="Chevron_Left_Circle">
                <path
                  d="M192.485,0C86.185,0,0,86.185,0,192.485C0,298.797,86.173,384.97,192.485,384.97S384.97,298.797,384.97,192.485
			C384.97,86.185,298.797,0,192.485,0z M192.485,361.282c-92.874,0-168.424-75.923-168.424-168.797S99.611,24.061,192.485,24.061
			s168.424,75.55,168.424,168.424S285.359,361.282,192.485,361.282z"
                />
                <path
                  d="M235.878,99.876c-4.704-4.74-12.319-4.74-17.011,0l-83.009,84.2c-4.572,4.62-4.584,12.56,0,17.191l82.997,84.2
			c4.704,4.74,12.319,4.74,17.011,0c4.704-4.752,4.704-12.439,0-17.191l-74.528-75.61l74.54-75.61
			C240.57,112.315,240.57,104.628,235.878,99.876z"
                />
              </g>
            </g>
          </svg>
        </button>
        <span class="text-xl"
          >{{ this.tickersPage }}/{{ this.tickers.length / 6 }}</span
        >
        <button
          :disabled="!tickersHasNextPage"
          @click="this.tickersPage = this.tickersPage + 1"
          class="border border-transparent focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500"
        >
          <svg
            class="w-6 h-6"
            version="1.1"
            id="Ebene_1"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            x="0px"
            y="0px"
            width="64px"
            height="64px"
            viewBox="0 0 64 64"
            enable-background="new 0 0 64 64"
            xml:space="preserve"
          >
            <g>
              <path
                d="M28.373,13.546c-0.803-0.758-2.068-0.722-2.827,0.081c-0.758,0.803-0.722,2.069,0.081,2.827L42.087,32l-16.46,15.546
		c-0.803,0.758-0.839,2.024-0.081,2.827C25.939,50.79,26.469,51,27,51c0.493,0,0.986-0.181,1.373-0.546l18-17
		C46.773,33.076,47,32.55,47,32s-0.227-1.076-0.627-1.454L28.373,13.546z"
              />
              <path
                d="M32,0C23.453,0,15.417,3.329,9.374,9.373C3.329,15.417,0,23.453,0,32s3.33,16.583,9.374,22.626
		C15.417,60.671,23.453,64,32,64s16.583-3.329,22.626-9.373C60.671,48.583,64,40.547,64,32s-3.33-16.583-9.374-22.626
		C48.583,3.329,40.547,0,32,0z M51.797,51.798C46.509,57.087,39.479,60,32,60s-14.509-2.913-19.798-8.202C6.913,46.51,4,39.479,4,32
		s2.913-14.51,8.203-19.798C17.491,6.913,24.521,4,32,4s14.509,2.913,19.798,8.202C57.087,17.49,60,24.521,60,32
		S57.087,46.51,51.797,51.798z"
              />
            </g>
          </svg>
        </button>
      </div>
    </div>
    <dl class="pb-8 grid grid-cols-1 gap-5 bg-gray-100 sm:grid-cols-3 px-4">
      <div
        v-for="t in paginatedTickers"
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
  </section>
</template>
