<script setup>
import { enableTracking } from '@vue/reactivity';
import {ref, onMounted} from 'vue';

const transcript = ref('');
const isRecording = ref(false);

const Recognition = window.SpeechRecognition||window.webkitSpeechRecognition;
const sr = new Recognition();
let lg="";

onMounted(()=>{
  sr.continuous = true;
  sr.interimResults = true;
  

  sr.onstart = ()=>{
    console.log('SR Started')
    isRecording.value =true
  }
  sr.onend = ()=>{
    console.log('SR Stopped');
    isRecording.value =false
  }
  sr.onresult = (evt)=>{
    for (let i =0; i< evt.results.lenght; i++){
      const result = evt.results[i];
      if (result.isFinal) CheckForCommand(result)
    }
    const t = Array.from(evt.results)
      .map(result=>result[0])
      .map(result=>result.transcript)
      .join(' ')
    transcript.value = t;
  }
})

const ToggleMic =()=>{
  if (isRecording.value) {
    sr.stop()
  } else{
    sr.start()
  }
}
const CheckForCommand = (result)=>{
  const t = result[0].transcript
  if (t.includes('stop recording')||t.includes('стоп запись')){
    sr.stop()
  } else if(
    t.includes('what is the time') ||
    t.includes('what\'s the time') ||
    t.includes('сколько время')
  ){
    sr.stop();
    alert(new Date().toLocaleTimeString())
    setTimeout(()=>sr.start(), 100)
  }
}

</script>

<template>
  <section>
    <h1>App Speach to Text</h1>
    <button
      :class="{mic:isRecording}"
      @click="ToggleMic"
    >
      🎤
    </button>
    <div class="content" v-text="transcript"></div>
  </section>
</template>

<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'Fira sans', sans-serif;
  }
  body{
    color: aliceblue;
    background: #281936;
  }
  section{
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
  }
  h1{
    text-align: center;
    font-size: 2.0em;
    margin-bottom: .5em;
  }
  button{
    width: 200px;
    height: 200px;
    border-radius: 50%;
    background-color: #fff;
    color: tomato;
    font-size: 3em;
    border: none;
    outline: none;
    text-decoration: none;
    margin-bottom: 1em;
  }
  button.mic{
    background-color: tomato;
  }
  button:active{
    background-color: #fff;
    border: 10px solid tomato;
    outline:2px;
  }
  .content{
    width: 100%;
    max-width: 800px;
    min-height: 200px;
    background: #ccc;
    border-radius: 15px;
    color:#111;
    font-size: 1.25em;
  }

</style>
