<script setup>
import {ref, onMounted, useTemplateRef,} from 'vue'
import axios from 'axios';


const colors = ref([])

const getHex = async () => {
  try {
    const response = await fetch('http://colormind.io/api/', {
      method: 'POST',
      body: JSON.stringify({
        model: "default"
      })
      // Don't set Content-Type header
    });
    
    const data = await response.json();
    console.log(data.result);
   
    let rgbColors = data.result
    colors.value = rgbColors.map((color) => rgbToHex(color)) 
    
  } catch (error) {
    console.error('Error:', error);
  }
};

const componentToHex = (c) => {
      var hex = c.toString(16);
  return hex.length == 1 ? "0" + hex : hex;
}
const rgbToHex = (arr) => {
    return "#" + componentToHex(arr[0]) + componentToHex(arr[1]) + componentToHex(arr[2]);
}

const generatePallete = () => {
    getHex()
}
const copyToClipboard = (index) => {
    // alert('hey')
    var copyText = document.getElementById(`myInput-${index}`);
    var display = document.getElementById('display');
    //const copyText = useTemplateRef('my-input')
  // Select the text field
  copyText.select();
  copyText.setSelectionRange(0, 99999); // For mobile devices

   // Copy the text inside the text field
  navigator.clipboard.writeText(copyText.value);

  // Alert the copied text
//   alert("Copied the text: " + copyText.value);
  display.innerHTML = "Code " + copyText.value + " copied to clipboard";
}
onMounted(() => {
  getHex()
})
</script>

<template>
    <div class="display">
         <span id="display"></span>
    </div>
   
   <div class="header">
    <h1>Color Pallete Generator</h1>
   </div>
    
    <div class="box-grid" v-if="colors.length > 0">
        <div @click="copyToClipboard(index)" ref="color" v-for="(color, index) in colors" :key="index" class="outer-box">
            <div class="box" :style="{ 'background-color': color}"></div>
            <span>{{ color }}</span>
            <input class="hidden" :id="`myInput-${index}`" type="text" :value="color"/>
        </div>
    </div>
    <div v-else>
        <div class="cta">
            <span class="loader"></span>
        </div>
        
    </div>
    <div class="cta">
         <button class="btn" @click="generatePallete">Generate Pallete</button>
    </div>

    <div class="cta">
        <span>Click to copy individual color.</span>
    </div>
   
</template>

<style scoped>

.box{
    height: 250px;

}
.cta{
    display:flex;
    justify-content: center;
    margin-top: 30px;
}
.display{
    display:flex;
    justify-content: center;
    background-color: #fff;
    border-radius: 10px;
}
.header{
    padding: 10px 0;
}

.btn{
    padding: 10px 20px;
    border:none;
    background-color: purple;
    color: #fff;
    border-radius: 5px;
}
.outer-box{
    padding: 4px;
    background-color: #fff;
    border-radius: 5px;
}
.box-grid{
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
    gap: 8px;
}
span{
    color: gray;
}
.hidden{
    display:none
}
.loader {
  width: 48px;
  height: 48px;
  border: 3px solid #FFF;
  border-radius: 50%;
  display: inline-block;
  position: relative;
  box-sizing: border-box;
  animation: rotation 1s linear infinite;
}
.loader::after {
  content: '';  
  box-sizing: border-box;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  width: 56px;
  height: 56px;
  border-radius: 50%;
  border: 3px solid;
  border-color: #FF3D00 transparent;
}

@keyframes rotation {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
} 
@media only screen and (max-width: 750px) {
  .box-grid{
        display: grid;
        grid-template-columns: auto;
        gap: 8px;
    }
}
</style>