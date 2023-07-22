<template>
    <div>
      <button @click="togglePlayback">{{ isPlaying ? 'Stop' : 'Play' }}</button>
      <input type="range" min="0" max="1" step="0.1" v-model="volume" />
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
        loading: false,
        error: '',
        isPlaying: false,
        currentMetadata: ''
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
        }
      }
    }
  }
  </script>
  