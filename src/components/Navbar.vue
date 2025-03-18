<template>
  <nav class="py-2 rounded-full m-4 px-5">
    <div class="container mx-auto flex items-center justify-between flex-wrap">

        <span>
          <img src="/paint.png" alt="Pal" class="w-8 h-8">
        </span>
    <span class="color-grad logo text-xl">
        PalettePro
    </span>
      <div class="flex-grow flex flex-row-reverse lg:items-center w-auto">
        <div>
          <a href="https://github.com/MaltineR/Color"
             class="inline-block text-sm p-2 leading-none  rounded text-white hover:border-transparent hover:text-white"
          >
            <!--<img src="@/assets/github.svg" width="20" height="20" class="inline text-white">-->
          </a>
        </div>
      </div>
    </div>
  </nav>
</template>

<script>

export default {
  name: 'Navbar',
  data() {
    return {
      navbarExpanded: true,
    };
  },
};

</script>

<style scoped>
.logo{
  font-family: 'Yellowtail', cursive;
}
</style>
