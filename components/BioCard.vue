<!-- BioCard.vue -->
<template>
    <v-card height="100%">
      <v-card-title class="justify-center">
        <v-avatar variant="elevated" size="100">
          <img :src="avatar" alt="Avatar">
        </v-avatar>
      </v-card-title>
      <v-card-text class="text-center">
        <p class="namelabel">{{ name }}</p>
        <p>{{ bio }}</p>
        <a :href="link">
          <v-btn text x-small :style="{ }">
            <v-icon left>mdi-twitter</v-icon>{{ at }}
          </v-btn>
        </a>       
      </v-card-text>
    </v-card>
  </template>
  
  <script>
  export default {
    props: {
      name: {
        type: String,
        required: true,
      },
      bio: {
        type: String,
        required: true,
      },
      avatar: {
        type: String,
        required: true,
      },
      link: {
        type: String,
        required: true,
      },
        at: {
            type: String,
            required: false,
        },
    },
  }
  </script>
  
    <style scoped>
    .namelabel {
      font-size: 1.5em;
      font-weight: bold;
        color: #ffffff;
      /* apply red glow around text */
        text-shadow: 0 0 5px #ff0000, 0 0 10px #ff0000;
      ;
    }
    </style>
