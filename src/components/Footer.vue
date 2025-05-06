<template>
  <div class="bg-indigo-200 bg-opacity-25 p-6 text-xs">
    <div class="text-gray-700">
      <ul>
        <li>PalettePro {{ year }}</li>
        <li>
          <a href="https://github.com/MaltineR/Color" class="footer-link">Maltine Rama</a>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Footer',
  data() {
    return {
      year: new Date().getFullYear(),
    };
  },
};
</script>
<style lang="postcss" scoped>
.footer-link{
  @apply hover:text-blue-500;
}
</style>
