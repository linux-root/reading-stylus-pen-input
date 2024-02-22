<template>
  <v-card class="mx-auto my-12" max-width="800">
   <canvas id="canvas1637" width="800" height="500"
           @pointerdown="onPointerDown"
           @pointerup="stopDrawing"
           @pointerout="stopDrawing"
           @pointermove="draw"
   />
    <v-card-item>
      <v-card-title>Draw Panel</v-card-title>
    </v-card-item>

    <v-divider class="mx-4 mb-1"></v-divider>
    <v-card-item>
      <v-card-subtitle>
        (x, y) = ({{lastX ? lastX : 'N/A'}}, {{lastY ? lastY : 'N/A'}})
      </v-card-subtitle>
    </v-card-item>

    <v-card-actions>
      <v-btn color="deep-purple" variant="text" @click="reset">Reset</v-btn>
      <v-btn color="deep-purple" variant="text" @click="save">Save</v-btn>
    </v-card-actions>
  </v-card>
</template>
<script setup>
import {onMounted, ref} from "vue";

let canvasContext = null
let canvas = null

onMounted(()=> {
  const canvasElement = document.getElementById('canvas1637');
  canvasContext = canvasElement.getContext('2d');
  canvasContext.strokeStyle = 'black'; // Set line color
  canvas = canvasElement
})

const reset = () => {
  canvasContext.clearRect(0, 0, canvas.width, canvas.height);
  console.log('reset panel')
}

const save = () => {
  console.log('save image')
  const dataURL = canvas.toDataURL('image/png');

  // Create a temporary anchor element
  const link = document.createElement('a');
  link.download = 'drawing.png'; // Set the filename
  link.href = dataURL; // Set the data URL as the href attribute

  // Programmatically trigger the download
  document.body.appendChild(link);
  link.click();

  // Clean up
  document.body.removeChild(link);
}
const lastX = ref(null)
const lastY = ref(null)
const isDrawing = ref(false)
const onPointerDown = (e) => {
  console.log('mouse down')
  lastX.value = e.offsetX || e.pageX - canvas.value.offsetLeft
  lastY.value = e.offsetY || e.pageY - canvas.value.offsetTop
  isDrawing.value = true
  console.log('Start drawing')
}


const stopDrawing = () => {
  console.log('stop drawing')
  isDrawing.value = false
}


const draw = (e) => {
  if (!isDrawing.value) return;
  console.log('drawing...')
  canvasContext.beginPath();
  canvasContext.moveTo(lastX.value, lastY.value);
  canvasContext.lineTo(e.offsetX || e.pageX - canvas.offsetLeft, e.offsetY || e.pageY - canvas.offsetTop);
  canvasContext.stroke();
  lastX.value = e.offsetX || e.pageX - canvas.offsetLeft
  lastY.value = e.offsetY || e.pageY - canvas.offsetTop;
}

</script>

<style scoped>
canvas {
  /* Disable intrinsic user agent touch behaviors (such as panning or zooming) */
  touch-action: none;
}
</style>