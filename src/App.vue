<template>
  <d-box class="main__content">
    <d-card
      title="Hey there this is my speech recognition software."
      desc="Thank you for testing this out."
    >
      <d-box class="inner__content">
        <d-box class="header__text">
          <d-text class="transcript" v-text="transcript"></d-text>
        </d-box>
        <d-box>
          <d-button
            responsive
            :colorScheme="primary"
            size="large"
            text="Record"
            :pill="true"
            @click="ToggleMic"
          ></d-button>
        </d-box>
      </d-box>
    </d-card>
  </d-box>
</template>

<script setup>
import { DBox, DCard, DText, DButton } from "@deposits/ui-kit-vue";
import { ref, onMounted } from "vue";
import VueSpeech from "vue-speech";

const transcript = ref("");
const isRecording = ref(false);

const Recognition = window.SpeechRecognition || window.webkitSpeechRecognition
const sr = new Recognition()

    onMounted(() => {
      sr.continuous = true
      sr.interimResults = true
      sr.onstart = () => {
        console.log('SR Started')
        isRecording.value = true
      }
      sr.onend = () => {
        console.log('SR Stopped')
        isRecording.value = false
      }
      sr.onresult = (evt) => {
        for (let i = 0; i < evt.results.length; i++) {
          const result = evt.results[i]
          if (result.isFinal) CheckForCommand(result)
        }
        const t = Array.from(evt.results)
            .map(result => result[0])
            .map(result => result.transcript)
            .join('')

        transcript.value = t
      }
    });
const CheckForCommand = (result) => {
  const t = result[0].transcript;
  if (t.includes("stop recording")) {
    sr.stop();
  } else if (t.includes("what is the time") || t.includes("what's the time")) {
    sr.stop();
    alert(new Date().toLocaleTimeString());
    setTimeout(() => sr.start(), 100);
  }
};
const ToggleMic = () => {
  if (isRecording.value) {
    sr.stop();
  } else {
    sr.start();
  }
};
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Helvetica Neue", "sans-serif";
}
body {
  background: #a8d7ef;
  color: #ffffff;
}
.main__content {
  padding-top: 1em;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.inner__content {
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: 1em;
  width: 100%;
}
.header__text {
  display: flex;
  flex-direction: row;
  justify-content: center;
}
</style>
