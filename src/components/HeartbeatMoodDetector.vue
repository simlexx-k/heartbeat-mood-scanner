<template>
  <div class="container">
    <h1>Heartbeat Mood Detector</h1>
    <div class="sensor-container">
      <div class="sensor" 
           @mousedown="startReading" 
           @mouseup="stopReading" 
           @mouseleave="stopReading"
           @touchstart="startReading"
           @touchend="stopReading"
           @touchcancel="stopReading"
           :class="{ 'active': isReading }">
        <svg class="fingerprint" viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
          <path d="M50,2.5C23.8,2.5,2.5,23.8,2.5,50c0,26.2,21.3,47.5,47.5,47.5c26.2,0,47.5-21.3,47.5-47.5C97.5,23.8,76.2,2.5,50,2.5z M50,92.5C26.6,92.5,7.5,73.4,7.5,50C7.5,26.6,26.6,7.5,50,7.5c23.4,0,42.5,19.1,42.5,42.5C92.5,73.4,73.4,92.5,50,92.5z"/>
          <path d="M50,25c-13.8,0-25,11.2-25,25c0,13.8,11.2,25,25,25c13.8,0,25-11.2,25-25C75,36.2,63.8,25,50,25z M50,70c-11,0-20-9-20-20c0-11,9-20,20-20c11,0,20,9,20,20C70,61,61,70,50,70z"/>
          <path d="M50,35c-8.3,0-15,6.7-15,15c0,8.3,6.7,15,15,15c8.3,0,15-6.7,15-15C65,41.7,58.3,35,50,35z M50,60c-5.5,0-10-4.5-10-10c0-5.5,4.5-10,10-10c5.5,0,10,4.5,10,10C60,55.5,55.5,60,50,60z"/>
          <path d="M50,45c-2.8,0-5,2.2-5,5c0,2.8,2.2,5,5,5c2.8,0,5-2.2,5-5C55,47.2,52.8,45,50,45z"/>
        </svg>
        <div class="scan-line" :class="{ 'scanning': isReading }"></div>
      </div>
      <p class="instruction" v-if="!isReading && !result">Place and hold your finger on the scanner</p>
      <p class="instruction" v-if="isReading">Keep your finger on the scanner...</p>
    </div>
    <div v-if="isReading" class="progress-container">
      <div class="progress-bar">
        <div class="progress" :style="{ width: progress + '%' }"></div>
      </div>
      <p class="reading-text">Reading your heartbeat...</p>
    </div>
    <transition name="fade">
      <div v-if="result" class="result">
        <h2>Your mood is:</h2>
        <p class="mood">{{ result }}</p>
        <button @click="reset" class="reset-button">Try Again</button>
      </div>
    </transition>
    <audio ref="scanSound" src="/scan-sound.m4a" preload="auto"></audio>
  </div>
</template>

<script>
export default {
  name: 'HeartbeatMoodDetector',
  data() {
    return {
      isReading: false,
      progress: 0,
      result: "",
      states: [
        "Njaa Budaa 🤣",
        "Hujaoga broo 🤣",
        "Masaa mbaya, chomoka!",
        "Kaa rada buda! 💀",
        "Uko juu sana! 🚀",
        "Mazee, umewaka moto! 🔥",
        "Chill bro, utashika form 😎",
        "Pole sana, umebeat vibaya 💥",
        "Kaa mapema utulie ✌️",
        "Sasa ni kufaint, bro 😵",
        "Mazee, uko juu ya mawingu 🌥️",
        "Umechoka mbaya, wacha utulie 😴",
        "Kuna noma, achana na hii story ⚠️",
        "Hapo kwa biceps uko freshi! 💪",
        "Shtuka, kimeumana! 🛑",
        "Bro, uko fiti sana, relax! 😎",
        "Zii, hiyo ni stress! 😫",
        "Eish, uko soft sana! 🤭"
      ],
      scanSound: null,
      scanInterval: null,
    };
  },
  mounted() {
    this.scanSound = this.$refs.scanSound;
  },
  methods: {
    startReading() {
      if (this.isReading || this.result) return;
      this.isReading = true;
      this.result = "";
      this.progress = 0;

      this.playSound();
      this.vibrate();

      this.scanInterval = setInterval(() => {
        if (this.progress >= 100) {
          this.completeReading();
        } else {
          this.progress += 1;
        }
      }, 30);
    },
    stopReading() {
      if (!this.isReading) return;
      this.isReading = false;
      clearInterval(this.scanInterval);
      this.progress = 0;
      this.stopSound();
    },
    completeReading() {
      clearInterval(this.scanInterval);
      this.showRandomState();
      this.isReading = false;
      this.stopSound();
    },
    showRandomState() {
      this.result = this.states[Math.floor(Math.random() * this.states.length)];
    },
    reset() {
      this.result = "";
      this.progress = 0;
    },
    playSound() {
      if (this.scanSound) {
        this.scanSound.currentTime = 0;
        this.scanSound.play();
      }
    },
    stopSound() {
      if (this.scanSound) {
        this.scanSound.pause();
        this.scanSound.currentTime = 0;
      }
    },
    vibrate() {
      if ('vibrate' in navigator) {
        // Vibrate for 200ms
        navigator.vibrate(200);
      }
    }
  }
};
</script>

<style scoped>
.container {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f5f5f5;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

h1 {
  color: #333;
  margin-bottom: 30px;
}

.sensor-container {
  position: relative;
  margin-bottom: 30px;
}

.sensor {
  position: relative;
  width: 200px;
  height: 200px;
  margin: 0 auto;
  cursor: pointer;
  background-color: #fff;
  border-radius: 50%;
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
  transition: all 0.3s ease;
  overflow: hidden;
}

.sensor:hover {
  transform: scale(1.05);
}

.sensor.active {
  background-color: #e0e0e0;
  box-shadow: 0 0 15px rgba(76, 175, 80, 0.5);
}

.fingerprint {
  width: 100%;
  height: 100%;
  fill: #333;
}

.scan-line {
  position: absolute;
  top: 0;
  left: -100%;
  width: 200%;
  height: 5px;
  background: linear-gradient(to right, transparent, #4CAF50, transparent);
  opacity: 0;
  transition: opacity 0.3s ease;
}

.scan-line.scanning {
  opacity: 1;
  animation: scan 3s linear infinite;
}

@keyframes scan {
  0% {
    top: 0;
  }
  100% {
    top: 100%;
  }
}

.instruction {
  margin-top: 15px;
  color: #666;
}

.progress-container {
  margin-bottom: 30px;
}

.progress-bar {
  width: 100%;
  height: 10px;
  background-color: #ddd;
  border-radius: 5px;
  overflow: hidden;
  margin-bottom: 10px;
}

.progress {
  height: 100%;
  background-color: #4CAF50;
  transition: width 0.3s ease;
}

.reading-text {
  color: #666;
  font-style: italic;
}

.result {
  background-color: #fff;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.result h2 {
  color: #333;
  margin-bottom: 10px;
}

.mood {
  font-size: 36px;
  margin-bottom: 20px;
}

.reset-button {
  background-color: #4CAF50;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.reset-button:hover {
  background-color: #45a049;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>