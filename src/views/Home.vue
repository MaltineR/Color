<template>
  <div class="screen-off lg:py-12 lg:relative w-full px-3 lg:mb-0 mb-10">
    <p
      class="text-center font-semibold text-gray-700 text-2xl"
      :style="{
        fontFamily: 'system-ui'
      }"
    >
    Gjej Ngjyrat E Përsosura Për Projektin Tënd!
    </p>
    <p
      class="text-left font-thin text-gray-600 py-4 text-center text-l"
      :style="{
        fontFamily: 'system-ui'
      }"
    >
    Parapamje e Imazhit
    </p>

    <div class="flex flex-wrap">
      <div class="bg-gray-400 bg-opacity-25 rounded-lg w-full lg:mb-10 mb-2 relative lg:h-custom2 h-custom-mobile lg:p-10 p-2">
        <ImgDisplay />
      </div>
      <div class="bg-gray-400 bg-opacity-25 rounded-lg w-full">
        <Colors class="w-full p-4" />
      </div>
    </div>
  </div>
</template>
<script>
import Colors from '@/components/Colors.vue';
import ImgDisplay from '@/components/ImgDisplay.vue';

export default {
  name: 'Home',
  components: {
    Colors,
    ImgDisplay,
  },
};
</script>

<style scoped>
.screen-off{
  min-height: calc(100vh - 205px);
}
.border-custom{
  border-width: 2px;
  border-color: #D1D5DB;
  border-radius: 0.375rem
}
.h-custom-mobile{
  min-height: 200px;
}
</style>
