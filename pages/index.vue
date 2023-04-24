<template>
  <div>
    <!-- Existing iframe element -->
    <iframe src="https://store.steampowered.com/widget/774801/?t=Claw%20your%20way%20across%20exotic%20islands%20combining%20fluid%20movement%20with%20fast%20paced%20combat%20to%20become%20a%20Crab%20Champion%20in%20this%20third%20person%20shooter%20with%20roguelike%20elements." frameborder="0" width="100%" height="290"></iframe>
    
    <!-- Vuetify cards for displaying bios and avatars -->
    <v-row justify="center">
      <!-- Card for the first person -->
      <v-col cols="auto" style="width: 350px;">
        <BioCard
          :name="person1.name"
          :bio="person1.bio"
          :avatar="person1.avatar"
          :link="person1.link"
          :at="person1.at"
        />
      </v-col>
      <!-- Card for the second person -->
      <v-col cols="auto" style="width: 350px;">
        <BioCard
          :name="person2.name"
          :bio="person2.bio"
          :avatar="person2.avatar"
          :link="person2.link"
          :at="person2.at"
        />
      </v-col>
    </v-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // Data for two people with their bios and avatars
      person2: {
        name: 'Meeps',
        bio: 'I built this website',
        avatar: 'https://pbs.twimg.com/profile_images/1574594656137924608/o8YmvT9z_400x400.jpg',
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
}
</script>
