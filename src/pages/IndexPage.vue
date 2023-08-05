<template lang="">
  <div class="bg-gray-500 min-h-[100vh] flex items-center">
    <div class="mx-auto bg-white py-4 px-4 rounded-md">
      <div v-if="submitActive" class="text-lg font-bold text-center">
        {{ percentColor }}
      </div>
      <div class="flex gap-8 items-end justify-center">
        <div
          :class="['w-16 bg-red-200 h-16', { 'move-right': isMoving }]"
          :style="`background-color: ${backgroundColorRndom};  transform: translateX(${position}px); `"
        ></div>
        <div
          :class="['w-16 bg-red-200 h-16', { 'move-left': isMoving2 }]"
          :style="`background-color: ${backgroundColor}; transform: translateX(${position2}px);`"
        ></div>
      </div>

      <div v-if="!submitActive" class="text-center mt-4 text-xl font-bold">
        FIND RGB
      </div>
      <div v-if="!submitActive" class="flex items-center mx-4">
        <q-slider
          v-model="rvalue"
          :min="0"
          :max="255"
          color="red"
          class="w-64"
          thumb-size="40px"
          track-size="20px"
          @update:modelValue="updateRGB"
        />
        <div class="pl-2 text-xl font-bold">{{ rvalue }}</div>
      </div>

      <div v-if="!submitActive" class="flex items-center mx-4">
        <q-slider
          v-model="gvalue"
          :min="0"
          :max="255"
          color="green"
          class="w-64"
          thumb-size="40px"
          track-size="20px"
          @update:modelValue="updateRGB"
        />
        <div class="pl-2 text-xl font-bold">{{ gvalue }}</div>
      </div>

      <div v-if="!submitActive" class="flex items-center mx-4">
        <q-slider
          v-model="bvalue"
          :min="0"
          :max="255"
          color="blue"
          class="w-64"
          thumb-size="40px"
          track-size="20px"
          @update:modelValue="updateRGB"
        />
        <div class="pl-2 text-xl font-bold">{{ bvalue }}</div>
      </div>
      <div class="w-full flex justify-center items-center mt-2">
        <q-btn v-if="!submitActive" @click="submit" class="w-fit bg-white"
          >Submit</q-btn
        >

        <q-btn v-if="submitActive" @click="reset" class="w-fit bg-white"
          >ลองใหม่</q-btn
        >
      </div>
      <div class="text-[10px] text-right mt-4">Create by P'Tanakan</div>
    </div>
  </div>
</template>
<script setup>
import { ref, computed, onMounted } from 'vue';
const rvalue = ref(0);
const gvalue = ref(0);
const bvalue = ref(0);
const backgroundColor = ref('#CFCFCF');
const backgroundColorRndom = ref('#CFCFCF');
const percentColor = ref('');

const submitActive = ref(false);

const position = ref(0);
const position2 = ref(0);
const isMoving = ref(false);
const isMoving2 = ref(false);

function manhattanDistance(color1, color2) {
  const r1 = color1[0];
  const g1 = color1[1];
  const b1 = color1[2];

  const r2 = color2[0];
  const g2 = color2[1];
  const b2 = color2[2];

  const distance = Math.abs(r2 - r1) + Math.abs(g2 - g1) + Math.abs(b2 - b1);
  return distance;
}

const submit = () => {
  isMoving.value = true;
  position.value += 20;
  setTimeout(() => {
    isMoving.value = false;
  }, 500); // Adjust the time to match your animation duration.
  moveLeft();
  submitActive.value = true;
};

const reset = () => {
  position.value = 0;
  position2.value = 0;

  submitActive.value = false;
};

const moveLeft = () => {
  isMoving2.value = true;
  position2.value -= 15;
  setTimeout(() => {
    isMoving2.value = false;
  }, 500); // Adjust the time to match your animation duration.
  const color1 = [rvalue.value, gvalue.value, bvalue.value];
  const color2 = [randomR.value, randomG.value, randomB.value];

  const distance = manhattanDistance(color1, color2);
  const maxDistance = Math.sqrt(255 ** 2 + 255 ** 2 + 255 ** 2); // ค่าสูงสุดที่เป็นไปได้ของระยะห่างสี
  const percentage = ((maxDistance - distance) / maxDistance) * 100;
  percentColor.value = `เปอร์เซ็นต์ความใกล้เคียง: ${percentage.toFixed(2)}%`;
  console.log(`เปอร์เซ็นต์ความใกล้เคียง: ${percentage.toFixed(2)}%`);
};

const rgbToHex = (rgb) => {
  if (rgb.length !== 3) {
    throw new Error(
      'Invalid RGB array. The array should have exactly 3 elements.'
    );
  }

  const hexValue = rgb.reduce((acc, val) => {
    if (val < 0 || val > 255) {
      throw new Error(
        'Invalid RGB value. Each value should be between 0 and 255.'
      );
    }
    const hex = val.toString(16).padStart(2, '0');
    return acc + hex;
  }, '#');

  return hexValue.toUpperCase();
};

function getHexColor() {
  const rgbArray = [rvalue.value, gvalue.value, bvalue.value];
  return rgbToHex(rgbArray);
}

const myHexColor = ref(getHexColor());

const getDynamicClass = ref(`w-16 bg-${myHexColor.value} h-16`);
const ramdomColor = ref('');
function updateRGB() {
  backgroundColor.value = getHexColor();
  console.log(
    '🚀 ~ file: IndexPage.vue:91 ~ updateRGB ~ getDynamicClass.value:',
    getDynamicClass.value
  );
}

const randomR = ref('');
const randomG = ref('');
const randomB = ref('');

const randomRGB = () => {
  randomR.value = Math.floor(Math.random() * 255);
  randomG.value = Math.floor(Math.random() * 255);
  randomB.value = Math.floor(Math.random() * 255);
};

function getHexColorRandom() {
  const rgbArray = [randomR.value, randomG.value, randomB.value];
  return rgbToHex(rgbArray);
}

onMounted(() => {
  randomRGB();
  backgroundColorRndom.value = getHexColorRandom();
  console.log(backgroundColorRndom);
});
</script>
<style>
.move-right {
  transition: transform 0.5s ease; /* Adjust the transition duration and timing function as needed */
}
.move-left {
  transition: transform 0.5s ease; /* Adjust the transition duration and timing function as needed */
}
</style>
