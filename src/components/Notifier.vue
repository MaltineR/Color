<template>
  <div
    v-if="visible"
    class="fixed p-5 z-30 bg-gray-800 text-white rounded-md shadow-lg flex items-center justify-between w-80"
    :class="positionClass()"
  >
    <div>
      <h2 class="text-lg font-bold">Welcome!</h2>
      <p class="text-sm">Upload an image to extract colors. Click "X" to close.</p>
    </div>
    <button @click="hide" class="ml-4 px-2 py-1 bg-red-500 rounded">X</button>
  </div>
</template>

<script>
export default {
  name: 'Notifier',
  props: {
    position: {
      type: String,
      default: 'bl',
    },
  },
  data() {
    return {
      visible: true, // ✅ Ensure it's true so it appears when the page loads
    };
  },
  methods: {
    positionClass() {
      switch (this.position) {
        case 'br': return 'bottom-0 right-0';
        case 'bl': return 'bottom-0 left-0';
        case 'tl': return 'top-0 left-0';
        case 'tr': return 'top-0 right-0';
        default: return 'bottom-0 left-0';
      }
    },
    hide() {
      this.visible = false; // ✅ Hides when the "X" button is clicked
    },
  },
};
</script>
