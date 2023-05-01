<template>
  <div>
    <VApp>
      <v-navigation-drawer v-model="drawer" :mini-variant="miniVariant" :clipped="clipped" fixed app>
        <v-list>
          <v-list-item v-for="(item, i) in items" :key="i" :to="item.to" router exact>
            <template v-slot:prepend>
              <v-icon :icon="item.icon"></v-icon>
            </template>
            <v-list-item-title v-text="item.title"></v-list-item-title>
          </v-list-item>
          <!-- Vuetify cards for displaying bios and avatars -->

        </v-list>
        
      </v-navigation-drawer>
      <v-app-bar :clipped-left="clipped" fixed app>
          <v-app-bar-nav-icon @click.stop="drawer = !drawer" />

          <v-toolbar-title>{{ title }}</v-toolbar-title>
          
          <div class="version-dropdown" style="width: 40%; background-color: #60b3e3; padding: 20px;">
    <v-select :items="versions" label="Select Game Version" v-model="selectedVersion" 
      @update:model-value="onVersionChange($event)" variant="plain"></v-select>
  </div>

        </v-app-bar>
      <VMain>
        <div class="wave-container">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1600 300" preserveAspectRatio="none" class="wave">
            <path id="wavePath">
              <animate attributeName="d" dur="15s" repeatCount="indefinite"
                values="M0,200 C200,50 200,350 400,200 C600,50 600,350 800,200 C1000,50 1000,350 1200,200 C1400,50 1400,350 1600,200 V500 H0 Z;
                           M0,200 C200,100 200,300 400,200 C600,100 600,300 800,200 C1000,100 1000,300 1200,200 C1400,100 1400,300 1600,200 V500 H0 Z;
                           M0,200 C200,50 200,350 400,200 C600,50 600,350 800,200 C1000,50 1000,350 1200,200 C1400,50 1400,350 1600,200 V500 H0 Z" />
            </path>
          </svg>
        </div>
        <slot />
      </VMain>
    </VApp>
  </div>
</template>

<script>
import { computed } from 'vue'
import data from '~/assets/data/crab-data.json';
export default {
  name: 'DefaultLayout',
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-waves',
          title: 'Welcome',
          to: '/',
        },
        {
          icon: 'mdi-star',
          title: 'Perks',
          to: '/perks',
        },
        {
          icon: 'mdi-pistol',
          title: 'Weapon Mods',
          to: '/wmods',
        },
        {
          icon: 'mdi-bomb',
          title: 'Grenade Mods',
          to: '/gmods',
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Tony\'s Archive',
      selectedVersion: "NOT UPDATED",
      versions: [],
      gameinfo: null,
      person2: {
        name: 'Meeps',
        bio: 'I built this website',
        avatar: '/meeps.jpg',
        link: 'https://twitter.com/MeepsKitten',
        at: '@MeepsKitten'
      },
      person1: {
        name: 'Noisestorm',
        bio: 'Creator of Crab Champions',
        avatar: 'https://pbs.twimg.com/profile_images/581237238073294848/W-127oOR_400x400.jpg',
        link: 'https://twitter.com/NoisestormMusic',
        at: '@NoisestormMusic'
      },
    }
  },
  provide() {
    return {
      selectedVersion: computed(() => this.selectedVersion),
      selectedData: computed(() => this.gameinfo)
    };
  },

  mounted() {
    // Fetch data when the layout is mounted
    // Use the imported data directly
    this.versions = Object.keys(data).sort((b, a) => {
      return a.localeCompare(b, undefined, { numeric: true });
    });
    
    // Check if the stored version exists in the available versions
    if (this.versions.length > 0) {
      // Use the index 0 if the stored version doesn't exist
      this.selectedVersion = this.versions[0];
    }

    // Load the game info for the selected version
    this.loadGameInfo();
  },

  methods: {
    // Method to load game info based on the selected version
    loadGameInfo() {
      if (this.selectedVersion && data.hasOwnProperty(this.selectedVersion)) {
        // Get the game info for the selected version
        this.gameinfo = data[this.selectedVersion];
        //log
        console.log(this.selectedVersion);
      }
    },
    // Method to handle version selection change
    onVersionChange(newVersion) {
      console.log("Version changed to " + newVersion)
      // Update the selected version
      this.selectedVersion = newVersion;

      // Store the selected version in localStorage
      localStorage.setItem('selectedVersion', newVersion);

      // Load the game info for the selected version
      this.loadGameInfo();
    },
  },
}
</script>

<style scoped>
/* Set maximum width, padding, and font size for the dropdown */
.v-input {
  max-width: 100% !important;
  /* Adjust the value based on your preference */
  padding-top: 20px !important;
  /* Adjust the value based on your preference */
  font-size: 18px !important;
  /* Increase font size */
  background-color: #60b3e3;
}

@keyframes waveAnimation {
  0% {
    transform: translateX(0);
  }

  100% {
    transform: translateX(-50%);
  }
}

.wave-container {
  position: fixed;
  /* Make wave stay in place */
  bottom: -10px;
  left: 0;
  width: 200%;

  overflow: hidden;
  z-index: 0;

  /* Place wave behind perks */
}

.wave {
  fill: #60b3e3;
  /* Wave color */
  animation: waveAnimation 15s linear infinite;
  /* Wave animation */

}
</style>


