<template>
  <v-app dark>
    
    <v-navigation-drawer v-model="drawer" :mini-variant="miniVariant" :clipped="clipped" fixed app>
      <v-list>
        <v-list-item v-for="(item, i) in items" :key="i" :to="item.to" router exact>
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar :clipped-left="clipped" fixed app>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />

      <v-toolbar-title>{{ title }}</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-select class="version-dropdown" :items="versions" label="Select Game Version"
        v-model="selectedVersion"></v-select>
    </v-app-bar>
    <v-main>
      <div class="wave-container">
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1600 500" preserveAspectRatio="none" class="wave">
        <path id="wavePath">
          <animate attributeName="d" dur="15s" repeatCount="indefinite"
            values="M0,200 C200,50 200,350 400,200 C600,50 600,350 800,200 C1000,50 1000,350 1200,200 C1400,50 1400,350 1600,200 V500 H0 Z;
                           M0,200 C200,100 200,300 400,200 C600,100 600,300 800,200 C1000,100 1000,300 1200,200 C1400,100 1400,300 1600,200 V500 H0 Z;
                           M0,200 C200,50 200,350 400,200 C600,50 600,350 800,200 C1000,50 1000,350 1200,200 C1400,50 1400,350 1600,200 V500 H0 Z" />
        </path>
      </svg>
    </div>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
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
    }
  },
  provide() {
    return {
      selectedVersion: computed(() => this.selectedVersion)
    };
  },

  created() {
    // Fetch data when the layout is mounted
    // Use the imported data directly
    this.versions = Object.keys(data);
    this.gameinfo = data;
    if (this.versions.length > 0) {
      this.selectedVersion = this.versions[0];
      //this.onVersionChange();
    }
  },
}
</script>

<style scoped>
/* Set maximum width, padding, and font size for the dropdown */
.v-input {
  max-width: 30% !important;
  /* Adjust the value based on your preference */
  padding-top: 30px !important;
  /* Adjust the value based on your preference */
  font-size: 18px !important;
  /* Increase font size */
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
  z-index: 1;
  /* Place wave behind perks */
}

.wave {
  fill: #60b3e3;
  /* Wave color */
  animation: waveAnimation 15s linear infinite;
  /* Wave animation */
}
</style>



