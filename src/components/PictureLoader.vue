<template>
  <div class="mt-10 lg:block lg:p-0 p-8 lg:relative fixed z-10 w-full bottom-0 visible bg-white lg:bg-transparent lg:shadow-none lg:rounded-none shadow-lg rounded-t-lg" :class="mobileViewExpanded ? 'h-auto' : 'h-0'">
    <button class="h-5 w-5 absolute top-0 right-0 m-2 text-gray-600 focus:outline-none lg:hidden" @click="mobileViewExpanded = !mobileViewExpanded">
      {{ mobileViewExpanded ? '\u25BC' : '\u25B2' }}
    </button>
    <!--<form v-on:submit.prevent @submit="readImage(url)" class="flex lg:px-4 py-2">
      <input type="text" class="input-style" placeholder="Paste image url here or a word." v-model="url" />
    </form>-->
    <div class="flex lg:px-4 py-2">
      <input type="number" class="input-style" placeholder="Number of colors" v-model="quantity" />
    </div>
    <div class="flex flex-wrap lg:relative w-full lg:p-0">
      <div class="w-full lg:px-4 py-2">
        <label for="inputPicture" class="rounded-lg block bg-indigo-400 text-white px-4 py-2 cursor-pointer w-full text-center">
          Upload Image
        </label>
        <input class="hidden" id="inputPicture" type="file" ref="imgSrc" @change="readImage()" />
      </div>
      <div class="w-full lg:px-4 py-2">
        <button class="rounded-lg bg-gray-900 text-white px-4 py-2 w-full" @click="getDataImage" :disabled="pictureAvaibility" :class="{ 'opacity-50 cursor-not-allowed': pictureAvaibility }">
          {{ extracting ? 'Extracting...' : 'Extract Color' }}
        </button>
      </div>
      <div class="w-full lg:px-4 py-2">
        <button class="rounded-lg bg-gray-500 text-white px-6 py-1 w-auto mt-2 text-sm" @click="showHelp = true">
      Help
    </button>
      </div>
    </div>
    <!-- Help Modal -->
    <div v-if="showHelp" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-20">
      <div class="bg-white p-8 rounded-lg w-11/12 md:w-1/2 lg:w-1/3">
        <h2 class="text-2xl font-bold mb-4">How to Use This Website</h2>
        <p class="mb-4">
          Here are some instructions on how to use this website:
          <ul class="list-disc pl-5">
            <li>Upload an image using the "Upload Image" button.</li>
            <li>Enter the number of colors you want to extract (max 20).</li>
            <li>Click "Extract Color" to get the dominant colors from the image.</li>
          </ul>
        </p>
        <div class="video-container mb-4">
          <!-- Embed your video here -->
          <iframe width="100%" height="315" src="https://www.youtube.com/embed/your-video-id" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
        </div>
        <button class="rounded-lg bg-red-500 text-white px-4 py-2 w-full" @click="showHelp = false">
          Close
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import readPictureAsBase64 from '@/helper/FileReader';
import clusterColor from '@/modules/ClusterFvck';

const urlValidation = /http(s)?:\/\/(www\.)?[a-zA-Z0-9@:%._+~#=]{2,256}\.[a-z]{2,6}\b([-a-zA-Z0-9@:%_+.~#?&//=]*)/gi;

export default {
  name: 'PictureLoader',
  data() {
    return {
      quantity: null,
      imageObject: new Image(),
      canvas: document.createElement('canvas'),
      extracting: false,
      url: '',
      pictureAvaibility: true,
      mobileViewExpanded: true,
      showHelp: false, // Control visibility of the help modal
    };
  },
  mounted() {
    this.imageObject.crossOrigin = 'Anonymous';
    this.canvas.style.imageRendering = 'pixelated';
    this.pictureAvaibility = true;
  },
  watch: {
    quantity(newVal) {
      if (newVal > 20) {
        this.quantity = 20;
      }
      if (this.$store.getters.getImgSource) {
        this.pictureAvaibility = false;
      }
    },
  },
  methods: {
    readImage(url = null) {
      if (url) {
        const theUrl = url.match(urlValidation) ? url : `https://source.unsplash.com/800x600?${url}`;
        this.$store.dispatch('toggleIsFetchingImg');
        this.$store.dispatch('setImageSource', null);
        fetch(theUrl)
          .then((result) => result.blob())
          .then((image) => {
            readPictureAsBase64(image)
              .then((res) => {
                const base64Image = res.target.result;
                this.$store.dispatch('setImageSource', base64Image);
                this.imageObject.src = base64Image;
                this.pictureAvaibility = false;
                this.$store.dispatch('toggleIsFetchingImg');
              })
              .catch((error) => {
                this.$store.dispatch('toggleIsFetchingImg');
                console.error(error);
              });
          })
          .catch((error) => {
            console.error(error);
          });
      } else {
        const file = this.$refs.imgSrc;
        readPictureAsBase64(file.files[0])
          .then((res) => {
            const base64Image = res.target.result;
            this.$store.dispatch('setImageSource', base64Image);
            this.imageObject.src = base64Image;
            this.pictureAvaibility = false;
          })
          .catch((error) => {
            console.error(error);
          });
      }
    },
    getDataImage() {
      if (this.$store.getters.getImgSource) {
        this.extracting = true;
        this.$store.dispatch('setTopColors', []);
        this.drawImage();
        const imgData = this.canvas.getContext('2d').getImageData(0, 0, this.canvas.width, this.canvas.height);
        this.$store.dispatch('toggleExtraction');
        clusterColor(imgData.data, this.quantity)
          .then((res) => {
            this.extracting = false;
            this.$store.dispatch('toggleExtraction');
            this.$store.dispatch('setTopColors', res);
            this.pictureAvaibility = true;
          })
          .catch((e) => {
            this.extracting = false;
            this.$store.dispatch('toggleExtraction');
            console.error(e);
            this.pictureAvaibility = true;
          });
      }
    },
    drawImage() {
      let scaleDown = 1;
      this.imageObject.crossOrigin = 'Anonymous';
      if (this.imageObject.width >= 3000 || this.imageObject.height >= 3000) {
        scaleDown = 0.05;
      } else if (this.imageObject.width > 500 || this.imageObject.height > 500) {
        scaleDown = 0.1;
      }
      this.canvas.width = this.imageObject.width * scaleDown;
      this.canvas.height = this.imageObject.height * scaleDown;
      this.canvas.getContext('2d').drawImage(this.$store.getters.getImgDomObject, 0, 0, this.canvas.width, this.canvas.height);
    },
  },
};
</script>

<style lang="postcss" scoped>
.input-style {
  @apply rounded-full w-full py-2 px-4 m-auto bg-gray-300 border-2 border-opacity-0 focus:border-2 focus:border-indigo-400 focus:outline-none focus:bg-white placeholder-gray-700 text-gray-700;
}

.video-container {
  position: relative;
  padding-bottom: 56.25%; /* 16:9 aspect ratio */
  height: 0;
  overflow: hidden;
}

.video-container iframe {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
</style>
