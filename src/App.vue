<script setup>
import { ref } from 'vue';

import defaultImage from '@/assets/default.jpeg'

const preview = ref(defaultImage)
const image = ref(null)
const activeClass = ref(0)
const deg = ref(0)
const flipx = ref(1)
const flipy = ref(1)
const brightnessValue = ref(100)
const grayscaleValue = ref(0)
const saturateValue = ref(100)
const invertValue = ref(0)
const activeFilter = ref('Brightness')


const previewImage = (event) => {
  const input = event.target;
      if (input.files) {
        preview.value = URL.createObjectURL(input.files[0]);
        activeClass.value = 1
      }
}
const rotateImage = (degrees) => {
  deg.value += degrees; 
}
const flipxImage = () => {
  flipx.value = flipx.value === 1 ? -1 : 1
}

const flipyImage = () => {
  flipy.value = flipy.value === 1 ? -1 : 1
}

const changeFilter = (val) => {
  activeFilter.value = val
}

const rangeValue = () => {
  const retVal = activeFilter.value === 'Brightness' ? brightnessValue.value : activeFilter.value === 'Starturation' ? saturateValue.value : activeFilter.value === 'Inversion' ? invertValue.value : grayscaleValue.value
  return retVal.toString()
}

const maxVal = () => {
  return (activeFilter.value === 'Brightness' || activeFilter.value === 'Starturation') ? 200 : 100
}

const updateFilter = (e) => {
if (activeFilter.value === 'Brightness') {
  brightnessValue.value = e.target.value
} else if (activeFilter.value === 'Starturation') {
  saturateValue.value = e.target.value
} else if (activeFilter.value === 'Inversion') {
  invertValue.value = e.target.value
} else  {
  grayscaleValue.value = e.target.value
}
}

// rotate(deg.value*Math.PI/180) ,scale(flipx.value,flipy.value)



const resteB = () => {
   deg.value = 0
   flipx.value = 1
   flipy.value = 1
   brightnessValue.value = 100
   grayscaleValue.value = 0
   saturateValue.value = 100
   invertValue.value = 0
   activeFilter.value = 'Brightness'
}
const download = () => {
  
        const canvas = document.createElement('canvas')
        const ctx = canvas.getContext('2d');
        const link = document.createElement('a')
        canvas.width = image.value.naturalWidth;
        canvas.height = image.value.naturalHeight;
        ctx.filter = `brightness(${brightnessValue.value}%) grayscale(${grayscaleValue.value}%) invert(${invertValue.value}%) saturate(${saturateValue.value}%)`
        ctx.translate(canvas.width / 2, canvas.height / 2)
        if (deg.value !== 0) {
          ctx.rotate(deg.value*Math.PI/180) 
        }
        ctx.scale(flipx.value,flipy.value)
        ctx.drawImage(image.value, -canvas.width / 2, -canvas.height / 2, canvas.width, canvas.height);
        link.href = canvas.toDataURL();
        link.download = 'result.png';
        link.click();

    }


</script>

  <template>
  <section>
      <div class="container flex flex-col items-center justify-center h-screen">
          <div class="grid grid-cols-2 gap-8 w-2/3">
              <div  :class="activeClass === 1 ? ' ' :' before:absolute before:top-0 before:right-0 before:w-full before:h-full before:bg-white/50'" class="relative border bored-[#6c7481] p-6 rounded-md">
                  <h2 class="text-[#4a4a4a] font-semibold pb-6">Filters</h2>
                  <div class="grid grid-cols-2 gap-2 pb-4">
                    <button @click="changeFilter('Brightness')" :class="activeFilter === 'Brightness' ? 'bg-[#5373ee] text-white':''" class="border bored-[#6c7481] font-semibold text-[#6c7481] p-2 rounded-md">Brightness</button>
                    <button @click="changeFilter('Starturation')" :class="activeFilter === 'Starturation' ? 'bg-[#5373ee] text-white':''" class="border bored-[#6c7481] font-semibold text-[#6c7481] hover:bg-[#5373ee] hover:text-white p-2 rounded-md">Starturation</button>
                    <button @click="changeFilter('Inversion')" :class="activeFilter === 'Inversion' ? 'bg-[#5373ee] text-white':''" class="border bored-[#6c7481] font-semibold text-[#6c7481] hover:bg-[#5373ee] hover:text-white p-2 rounded-md">Inversion</button>
                    <button @click="changeFilter('Grayscale')" :class="activeFilter === 'Grayscale' ? 'bg-[#5373ee] text-white':''" class="border bored-[#6c7481] font-semibold text-[#6c7481] hover:bg-[#5373ee] hover:text-white p-2 rounded-md">Grayscale</button>
                  </div>
              <div class="flex items-center justify-between py-4">
                  <h3 class="font-semibold text-[#6c7481]">{{activeFilter}}</h3>
                  <span class="font-semibold text-[#6c7481]">{{rangeValue()}}%</span>
              </div>
              <input :value="rangeValue()" type="range" class="w-full" min="0" :max="maxVal()" @change="updateFilter">
              <h2 class="text-[#4a4a4a] font-semibold pb-4 pt-6">Rotate & Flip</h2>
              <div class="grid grid-cols-4 gap-4 pb-4">
                <button @click="rotateImage(90)" class="border bored-[#6c7481] rounded-md  p-2"><box-icon name='rotate-left' color="#6c7481" ></box-icon></button>
                <button @click="rotateImage(-90)" class="border bored-[#6c7481] rounded-md  p-2"><box-icon name='rotate-right' color="#6c7481" ></box-icon></button>
                <button @click="flipxImage()" class="border bored-[#6c7481] rounded-md  p-2"><box-icon name='reflect-vertical' color="#6c7481" ></box-icon></button>
                <button @click="flipyImage()"  class="border bored-[#6c7481] rounded-md p-2"><box-icon name='reflect-horizontal' color="#6c7481" ></box-icon></button>
              </div>
              </div>
              <div class="">
                  <img ref="image" :style="{ transform: `rotate(${deg}deg) scaleX(${flipx}) scaleY(${flipy})`,filter: `brightness(${brightnessValue}%) grayscale(${grayscaleValue}%) invert(${invertValue}%) saturate(${saturateValue}%)` }" :src="preview" class="h-[410px] object-cover">
              </div>
              <button @click="resteB" class="text-[#4a4a4a] border border-[#737574] rounded-md p-2 w-1/2 disabled:opacity-75 disabled:cursor-not-allowed" :disabled="!activeClass">REST FILTERS</button>
              <div class="flex gap-4 justify-end px-2">
                  <label  class="text-white border border-[#737574] rounded-md py-4 px-4 text-center bg-[#6d737f] cursor-pointer">CHOOSE IMAGE<input accept="image/*" @change="previewImage" type="file" name="" id="" class="hidden"></label>
                  <button @click="download" class="text-white border border-[#737574] rounded-md py-4 px-4 bg-[#5372ef] disabled:opacity-75 disabled:cursor-not-allowed" :disabled="!activeClass">SAVE IMAGE</button>
              </div>
          </div>
      </div>
  </section>
  </template>

<style scoped>

</style>
