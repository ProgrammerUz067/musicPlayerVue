<template>
  <div class="music-player">
    <h1>Vue Music Player</h1>

    <!-- Display Current Track -->
    <div class="track-info">
      <h2>{{ currentTrack.title }}</h2>
      <p>{{ currentTrack.artist }}</p>
    </div>

    <!-- Audio Controls -->
    <div class="controls">
      <button @click="prevTrack">⏮</button>
      <button @click="togglePlayPause">
        {{ isPlaying ? "⏸" : "▶" }}
      </button>
      <button @click="nextTrack">⏭</button>
    </div>

    <!-- Progress Bar -->
    <div class="progress">
      <span>{{ formatTime(currentTime) }}</span>
      <input
        type="range"
        min="0"
        :max="duration"
        step="1"
        v-model="currentTime"
        @input="seek"
      />
      <span>{{ formatTime(duration) }}</span>
    </div>

    <!-- Volume Control -->
    <div class="volume-control">
      <label for="volume">Volume:</label>
      <input
        type="range"
        id="volume"
        min="0"
        max="1"
        step="0.01"
        v-model="volume"
        @input="setVolume"
      />
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isPlaying: false,
      currentTrackIndex: 0,
      currentTime: 0,
      duration: 0,
      volume: 0.5,
      audio: null,
      tracks: [
        {
          title: "Track 1",
          artist: "Artist 1",
          url: "path-to-your-audio-file-1.mp3",
        },
        {
          title: "Track 2",
          artist: "Artist 2",
          url: "path-to-your-audio-file-2.mp3",
        },
        {
          title: "Track 3",
          artist: "Artist 3",
          url: "path-to-your-audio-file-3.mp3",
        },
      ],
    };
  },
  computed: {
    currentTrack() {
      return this.tracks[this.currentTrackIndex];
    },
  },
  methods: {
    togglePlayPause() {
      if (this.isPlaying) {
        this.audio.pause();
      } else {
        this.audio.play();
      }
      this.isPlaying = !this.isPlaying;
    },
    prevTrack() {
      this.currentTrackIndex =
        (this.currentTrackIndex - 1 + this.tracks.length) % this.tracks.length;
      this.loadTrack();
    },
    nextTrack() {
      this.currentTrackIndex =
        (this.currentTrackIndex + 1) % this.tracks.length;
      this.loadTrack();
    },
    loadTrack() {
      if (this.audio) {
        this.audio.pause();
      }
      this.audio = new Audio(this.currentTrack.url);
      this.audio.volume = this.volume;

      this.audio.addEventListener("loadedmetadata", () => {
        this.duration = this.audio.duration;
      });

      this.audio.addEventListener("timeupdate", () => {
        this.currentTime = this.audio.currentTime;
      });

      this.audio.addEventListener("ended", () => {
        this.nextTrack();
      });

      if (this.isPlaying) {
        this.audio.play();
      }
    },
    seek() {
      this.audio.currentTime = this.currentTime;
    },
    setVolume() {
      this.audio.volume = this.volume;
    },
    formatTime(time) {
      const minutes = Math.floor(time / 60);
      const seconds = Math.floor(time % 60)
        .toString()
        .padStart(2, "0");
      return `${minutes}:${seconds}`;
    },
  },
  mounted() {
    this.loadTrack();
  },
};
</script>

<style scoped>
.music-player {
  text-align: center;
  font-family: Arial, sans-serif;
  max-width: 400px;
  margin: 50px auto;
  background: #262626;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}
.track-info {
  margin-bottom: 20px;
}
.controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 20px 0;
}
button {
  background: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 10px 20px;
  cursor: pointer;
}
button:hover {
  background: #0056b3;
}
.progress {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin: 20px 0;
}
input[type="range"] {
  flex: 1;
  margin: 0 10px;
}
.volume-control {
  margin-top: 20px;
}
</style>
