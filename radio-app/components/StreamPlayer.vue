<template>
    <div id="player">
      <button id="play-stop-btn" @click="togglePlayback">{{ isPlaying ? 'Stop' : 'Play' }}</button>
      <button v-if="!showVolumeSlider" @click="toggleMute">{{ isMuted ? 'Unmute' : 'Mute' }}</button>
      <input v-if="showVolumeSlider" id="volume-slider" type="range" min="0" max="1" step="0.1" v-model="volume" />
      <div id="progress-bar" @click="setProgress">
        <div id="progress" :style="{ width: `${progress}%` }"></div>
      </div>
      <p v-if="loading">Loading...</p>
      <p v-if="error">{{ error }}</p>
      <p>{{ currentMetadata }}</p>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        audio: null,
        volume: 0.5,
        isMuted: false,
        loading: false,
        error: '',
        isPlaying: false,
        currentMetadata: '',
        showVolumeSlider: true,
        progress: 0
      }
    },
    created() {
      if (typeof navigator !== 'undefined') {
        this.showVolumeSlider = !(/iPad|iPhone|iPod/.test(navigator.userAgent) && !window.MSStream);
      }
    },
    watch: {
      volume(newVolume) {
        if (this.audio) {
          this.audio.volume = newVolume;
        }
      }
    },
    methods: {
      togglePlayback() {
        if (this.audio && !this.audio.paused) {
          this.stopStream();
        } else {
          this.playStream();
        }
      },
      toggleMute() {
        if (this.audio) {
          this.isMuted = !this.isMuted;
          this.audio.muted = this.isMuted;
        }
      },
      playStream() {
        this.loading = true;
        this.error = '';
        this.audio = new Audio('https://ice41.securenetsystems.net/KWVH');
        this.audio.volume = this.volume;
        this.audio.play();
        this.isPlaying = true;
  
        this.audio.oncanplay = () => {
          this.loading = false;
        };
  
        this.audio.ontimeupdate = () => {
          this.progress = (this.audio.currentTime / this.audio.duration) * 100;
        };
  
        this.audio.onerror = () => {
          this.loading = false;
          this.isPlaying = false;
          this.error = 'An error occurred while trying to play the audio stream.';
        };
  
        // replace this with the real way to get metadata from the stream
        this.currentMetadata = 'Example Metadata';
      },
      stopStream() {
        if (this.audio) {
          this.audio.pause();
          this.audio.currentTime = 0;
          this.isPlaying = false;
          this.progress = 0;
        }
      },
      setProgress(event) {
        const clickX = event.clientX;
        const width = this.$refs.progress.offsetWidth;
        const duration = this.audio.duration;
        this.audio.currentTime = (clickX / width) * duration;
      }
    }
  }
  </script>
  
  <style scoped>
  #player {
    width: 400px;
    margin: 0 auto;
    text-align: center;
    background-color: #f2f2f2;
    padding: 20px;
    border-radius: 10px;
  }
  
  #play-stop-btn {
    font-size: 20px;
    padding: 10px 20px;
    margin: 10px;
  }
  
  #volume-slider {
    width: 100%;
  }
  
  #progress-bar {
    width: 100%;
    height: 20px;
    background: #ddd;
    margin: 20px 0;
    position: relative;
  }
  
  #progress {
    height: 100%;
    background: #333;
    position: absolute;
    left: 0;
    top: 0;
  }
  </style>
  