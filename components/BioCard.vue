<!-- BioCard.vue -->
<template >
  <v-card height="100%" width="100%" class="text-center" :color="ccolor">
    <v-card-title>
      <v-avatar height="100%" width="100%" variant="elevated" size="100" class="align-self-center">
        <img :src="avatar" alt="Avatar" cover class="pfp-img">
      </v-avatar>
    </v-card-title>
    <v-card-text class="text-center">
      <p class="namelabel">{{ name }}</p>
      <p>{{ bio }}</p>
      <a :href="link" target="_blank">
        <v-btn text x-small style="margin-top: 0.5em">
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
        ccolor: {
            type: String,
            required: false,
            default: "surface",
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
        text-shadow: 0 0 5px #0f172a, 0 0 10px #0f172a;
      ;
      margin-bottom: 0.2em;
      margin-top: -0.2em;

    }

    .pfp-img {
      width: 100%;
    }

    
    </style>
