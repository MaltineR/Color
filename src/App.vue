<template>
  <div id="app">
    <div class="flex">
      <div class="lg:w-1/6 w-0 bg-gray-100 lg:visible invisible dark:bg-gray-800">
        <navbar />
        <pic-loader />
      </div>
      <div class="lg:w-4/6 w-full m-5 bg-indigo-100 rounded-xl dark:bg-gray-700">
        <div class="container px-5 py-10 mx-auto">
          <router-view />
        </div>
        <my-footer class="hidden lg:block rounded-b-xl dark:bg-gray-700" />
      </div>
      <div
        class="lg:w-1/5 lg:block lg:py-5 lg:pr-5 p-5"
        :class="colorRecomendation ? 'fixed z-20 w-full h-screen bg-indigo-100 dark:bg-gray-700' : 'hidden'"
      >
        <div class="rounded-xl bg-indigo-200 bg-opacity-25 custom-right-sidebar p-5 overflow-y-scroll dark:bg-gray-600">
          <ColorRecomendation />
        </div>
      </div>
    </div>
    <div
      class="lg:hidden z-30"
      :class="colorRecomendation ? 'fixed w-full flex justify-center bottom-0' : 'fixed right-0 top-0 my-16'"
    >
      <button
        class="h-12 w-12 bg-gray-100 focus:outline-none text-gray-700 font-bold text-center dark:bg-gray-800 dark:text-gray-200"
        :class="colorRecomendation ? 'rounded-full mb-3 shadow-xl' : 'shadow-xl rounded-l-lg'"
        @click="colorRecomendation = !colorRecomendation"
      >
        {{ colorRecomendation ? '&#10005;' : '&#9776;' }}
      </button>
    </div>
    <notifier
      v-if="getUpdateAvaibility"
      class="text-center lg:mb-5 lg:ml-5 bg-indigo-200 lg:w-auto w-full lg:rounded-lg border border-indigo-200 dark:bg-gray-600 dark:border-gray-600"
    >
      <button class="text-xs p-1 focus:outline-none dark:text-gray-200" @click="reload()">
        Update i disponueshëm, ju lutem rifreskoni.
      </button>
    </notifier>

    <!-- Dark Mode Switch -->
    <label class="dark-mode-switch">
      <input type="checkbox" :checked="isDarkMode" @change="toggleDarkMode" />
      <span class="slider round"></span>
    </label>
  </div>
</template>

<script>
import { mapGetters } from 'vuex';
import Navbar from '@/components/Navbar.vue';
import MyFooter from '@/components/Footer.vue';
import PicLoader from '@/components/PictureLoader.vue';
import ColorRecomendation from '@/components/ColorsRecomendation.vue';
import Notifier from '@/components/Notifier.vue';

export default {
  name: 'App',
  components: {
    Navbar,
    MyFooter,
    PicLoader,
    ColorRecomendation,
    Notifier,
  },
  computed: {
    ...mapGetters(['getUpdateAvaibility']),
  },
  data() {
    return {
      colorRecomendation: false,
      isDarkMode: false, // Default to light mode
    };
  },
  methods: {
    reload() {
      window.location.reload();
    },
    toggleDarkMode() {
      this.isDarkMode = !this.isDarkMode;
      if (this.isDarkMode) {
        document.documentElement.classList.add('dark');
        localStorage.setItem('darkMode', 'enabled');
      } else {
        document.documentElement.classList.remove('dark');
        localStorage.setItem('darkMode', 'disabled');
      }
    },
  },
  mounted() {
    // Check localStorage for user preference
    const darkMode = localStorage.getItem('darkMode');
    if (darkMode === 'enabled') {
      this.isDarkMode = true;
      document.documentElement.classList.add('dark');
    }
    // No else case needed - default is light mode
  },
};
</script>

<style>
/* Global Styles */
body {
  background-color: #f7fafc;
  color: #4a5568;
}

.dark body {
  background-color: #1a202c;
  color: #e2e8f0;
}

/* Custom Right Sidebar */
.custom-right-sidebar {
  height: calc(100vh - 40px);
  -ms-overflow-style: none; /* IE and Edge */
  scrollbar-width: none; /* Firefox */
}

.custom-right-sidebar::-webkit-scrollbar {
  display: none;
}

/* Shimmer Animation */
.animate {
  animation: shimmer 2s infinite linear;
  background: linear-gradient(90deg, #eff1f3 4%, #e9e8e8 25%, #eff1f3 36%);
  background-size: 1000px 100%;
}

@keyframes shimmer {
  0% {
    background-position: -1000px 0;
  }
  100% {
    background-position: 1000px 0;
  }
}

/* Dark Mode Switch */
.dark-mode-switch {
  position: fixed;
  bottom: 1.25rem; /* bottom-5 */
  left: 1.25rem; /* Changed from right to left */
  z-index: 50; /* z-50 */
  display: inline-block;
  width: 51px;
  height: 20px;
}

.dark-mode-switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  transition: 0.4s;
  border-radius: 10px;
}

.slider:before {
  position: absolute;
  content: "";
  height: 13px;
  width: 13px;
  left: 4px;
  bottom: 4px;
  background-color: white;
  transition: 0.4s;
  border-radius: 50%;
}

input:checked + .slider {
  background-color: #2196F3;
}

input:checked + .slider:before {
  transform: translateX(26px);
}

/* Rounded sliders */
.slider.round {
  border-radius: 30px;
}

.slider.round:before {
  border-radius: 50%;
}
</style>
