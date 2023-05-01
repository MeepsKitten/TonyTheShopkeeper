<template>
  <div>
    <!-- Game title/logo -->
    <v-row justify="center">
      <v-col cols="auto">
        <img src="/CrabChampions_Text.png" alt="Crab Champions Logo" height="100">
      </v-col>
    </v-row>

    <!-- Carousel of game screenshots -->
    <v-carousel hide-delimiters ref="carousel" v-model="activeSlide" class="player">
    <v-carousel-item
      v-for="(item, index) in displayedScreenshots"
      :key="index"
      @click="onCarouselItemClick(item)"
      class="citem"
    >
      <template v-if="item.type === 'image'">
        <img :src="item.src" style="object-fit: cover; width: 100%; height: 100%;">
      </template>
      <template v-else>
        <video
          :src="item.src"
          ref="videoElement"
          muted
          autoplay
          controls
          style="object-fit: scale-down; width: 100%; height: 100%;"
          @ended="onVideoEnded"
        ></video>
      </template>
    </v-carousel-item>
  </v-carousel>
    
    <!-- Buy on Steam button -->
    <v-row justify="center">
      <v-col cols="auto">
        <v-btn href="https://store.steampowered.com/app/774801/Crab_Champions/" target="_blank" class="glow-button"
          color="background" size="x-large">
          Buy on Steam
        </v-btn>
      </v-col>
    </v-row>

    <!-- Vuetify cards for displaying bios and avatars -->
    <v-row justify="center">
      <!-- Card for the first person -->
      <v-col cols="auto" style="width: 350px;">
        <BioCard :name="person11.name" :bio="person11.bio" :avatar="person11.avatar" :link="person11.link" :at="person11.at" width="100%"/>
      </v-col>
      <!-- Card for the second person -->
      <v-col cols="auto" style="width: 350px;">
        <BioCard :name="person22.name" :bio="person22.bio" :avatar="person22.avatar" :link="person22.link" :at="person22.at" width="100%" />
      </v-col>
    </v-row>
  </div>
</template>

<script>
import BioCard from '~/components/BioCard.vue';

export default {
  components: {
    BioCard,
  },
  data() {
    return {
      // Data for two people with their bios and avatars
      person22: {
        name: 'Meeps',
        bio: 'I built this website',
        avatar: '/meeps.jpg',
        link: 'https://twitter.com/MeepsKitten',
        at: '@MeepsKitten'
      },
      person11: {
        name: 'Noisestorm',
        bio: 'Creator of Crab Champions',
        avatar: 'https://pbs.twimg.com/profile_images/581237238073294848/W-127oOR_400x400.jpg',
        link: 'https://twitter.com/NoisestormMusic',
        at: '@NoisestormMusic'
      },
      // Array of game screenshots
      screenshots: [
        { type: 'video', src: 'https://cdn.cloudflare.steamstatic.com/steam/apps/256936748/movie480_vp9.webm?t=1679355936' },
        { type: 'video', src: 'https://cdn.cloudflare.steamstatic.com/steam/apps/256939016/movie480_vp9.webm?t=1680394902' },
        { type: 'video', src: 'https://cdn.cloudflare.steamstatic.com/steam/apps/256941128/movie480_vp9.webm?t=1681480284' },
        { type: 'image', src: 'https://cdn.cloudflare.steamstatic.com/steam/apps/774801/ss_bc137c37dbade4be58b2f04e6026ed17906b3daf.1920x1080.jpg?t=1681480286' },
        { type: 'image', src: 'https://cdn.cloudflare.steamstatic.com/steam/apps/774801/ss_00121cb5e13f649e602a8d158d77150d690b09f2.1920x1080.jpg?t=1681480286' },
        { type: 'image', src: 'https://cdn.cloudflare.steamstatic.com/steam/apps/774801/ss_e3342d9b3c3591fb062a567d560e2e6d0f4bc651.1920x1080.jpg?t=1681480286' },
        { type: 'image', src: 'https://cdn.cloudflare.steamstatic.com/steam/apps/774801/ss_d0e420f4474399bf343109a1b216f9610bdf70ea.1920x1080.jpg?t=1681480286' },
        { type: 'image', src: 'https://cdn.cloudflare.steamstatic.com/steam/apps/774801/ss_781c4177ac5606a4bed396e26cc80b380c0f4aae.1920x1080.jpg?t=1681480286' },
        { type: 'image', src: 'https://cdn.cloudflare.steamstatic.com/steam/apps/774801/ss_71756538faf728680a3a57331f6a718404ac37d6.1920x1080.jpg?t=1681480286' },
        { type: 'image', src: 'https://cdn.cloudflare.steamstatic.com/steam/apps/774801/ss_ec1d79305f3c7d60f05f954a5c55cebf4d3d4bea.1920x1080.jpg?t=1681480286' },
        { type: 'image', src: 'https://cdn.cloudflare.steamstatic.com/steam/apps/774801/ss_93d49e62ff9f94acf65332af12816173c4b145c0.1920x1080.jpg?t=1681480286' },
        { type: 'image', src: 'https://cdn.cloudflare.steamstatic.com/steam/apps/774801/ss_12a27ad25606ef94a99ec6e8029fe69dcfeb1b7a.1920x1080.jpg?t=1681480286' },
      ],
      activeSlide: 0,
    }
  },
  computed: {
    displayedScreenshots() {
      // Returns only the screenshots that are images or only the screenshots that are videos
      return this.screenshots.filter(item => this.isImage(item) || this.isVideo(item));
    }
  },
  methods: {
    isImage(item) {
      return item.type === 'image';
    },
    isVideo(item) {
      return item.type === 'video';
    },
    onVideoEnded() {
      console.log('video ended');  
      // Advance the carousel to the next item when the video ends
      this.activeSlide = (this.activeSlide + 1) % this.displayedScreenshots.length;
      },
  }
}
</script>

<style scoped>
.glow-button {
  position: relative;
  z-index: 1;
  border: 2px solid rgba(0, 0, 0, 0.2);
  animation: glow 2s ease-in-out infinite;
}

.glow-button:hover {
  border: 2px solid rgba(0, 0, 0, 1);
}


@keyframes glow {
  0% {
    box-shadow: 0 0 10px #60b3e3;
  }

  50% {
    box-shadow: 0 0 20px #60b3e3;
  }

  100% {
    box-shadow: 0 0 10px #60b3e3;
  }
}

.player
{
  margin-bottom: 1vh;
}
.citem
{
  width:100%;
  height:100%;
  /*drop shadow*/
  -webkit-box-shadow: 0px 0px 5px 0px rgba(0,0,0,0.75);
}
.glow-button::after {
  content: "";
  position: absolute;
  z-index: -1;
  box-shadow: 0 0 20px 5px $vuetify.theme.primary;
  opacity: 0;
  transition: opacity 1s ease-in-out;
  /* Increase the transition duration to 1 second */
}

.glow-button:hover::after {
  opacity: 1;
}
</style>
