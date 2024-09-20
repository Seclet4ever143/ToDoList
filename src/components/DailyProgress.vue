<template>
  <v-col cols="12" md="4" class="d-flex flex-column">
    <v-card class="pa-4 flex-grow-1" color="secondary">
      <v-card-title class="headline">Daily Progress</v-card-title>
      <v-divider></v-divider>

      <div class="progress-container mb-4">
        <v-progress-linear
          :value="progress"
          height="25"
          class="bg" :style="{ width: progress + '%' }"
        >
          <template v-slot:default>
            <span class="progress-label">{{ Math.round(progress) }}%</span>
          </template>
        </v-progress-linear>
      </div>

      <v-card-title>Tasks</v-card-title>
      <v-divider></v-divider>

      <v-list class="bg">
        <v-list-item @click="selectCategory('Personal')">
          <v-list-item-content>
            <v-list-item-title>Personal</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-list-item @click="selectCategory('Work')">
          <v-list-item-content>
            <v-list-item-title>Work</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-list-item @click="selectCategory('Health')">
          <v-list-item-content>
            <v-list-item-title>Health</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-list-item @click="selectCategory('Social')">
          <v-list-item-content>
            <v-list-item-title>Social</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-card>
  </v-col>
</template>

<script>
export default {
  props: ['progress'],
  methods: {
    selectCategory(category) {
      this.$emit('select-category', category);
    }
  }
};
</script>

<style scoped>
.progress-container {
  position: relative;
  border: 1px solid black;
  border-radius: 5px;
}

.progress-label {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  font-weight: bold;
  color: white;
  z-index: 1;
}

.bg {
  background-color: rgba(0, 255, 255, 0.573);
  border-radius: 5px;
}
</style>
