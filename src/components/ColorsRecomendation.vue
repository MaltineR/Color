<template>
  <div>
    <input
      class="rounded-full w-full py-2 px-4 m-auto bg-gray-100 focus:outline-none placeholder-gray-400 text-gray-700 mb-2"
      v-model="keyword"
      type="search"
      placeholder="Enter a niche ex:Food"
      @input="searchData()"
    >
    <!--<p class="text-xs text-center text-gray-600 mb-5">
      Enter your Website niche, and we'll recommend the best color palette tailored to your industry.
    </p>-->
    <div v-if="arrayOfColors.length > 0">
      <Palettes
        v-for="(palettes, key) in arrayOfColors"
        :key="key"
        :colorsHex="palettes"
      />
    <p class="text-xs text-center text-gray-600 mb-5">
      Enter your Website niche, and we'll recommend the best color palette tailored to your industry.
    </p>
    </div>
    <div v-else>
      <div v-if="isFetching" class="text-xs p-1 text-center">
        Looking palettes for <b>{{ keyword }}</b> ...
      </div>
      <div v-else-if="keyword">
        <p class="text-xs text-center p-1">
          We are sorry, currently no data for <b>{{ keyword }}</b>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import Palettes from '@/components/palettes.vue';
import { debounce } from '@/helper/CommonHelper';

export default {
  name: 'ColorRecomendation',
  components: {
    Palettes,
  },
  data() {
    return {
      keyword: null,
      arrayOfColors: [
        ['#bffb32ff', '#b7f738ff', '#9de748ff', '#8cdc52ff', '#5dc378ff'],
      ],
      isFetching: false,
    };
  },
  methods: {
    searchData: debounce(function () {
      if (this.keyword) {
        const url = `/colors_data/${this.keyword.toLowerCase()}.json`;
        this.isFetching = true;
        this.arrayOfColors = [];
        fetch(url)
          .then((result) => {
            this.isFetching = false;
            if (result.ok) {
              return result.json();
            }
            return false;
          })
          .then((data) => {
            if (data) {
              this.arrayOfColors = data;
            } else {
              this.arrayOfColors = [];
            }
          })
          .catch((error) => {
            this.isFetching = false;
            console.log('error: ', error);
          });
      }
    }, 850),
  },
};
</script>
