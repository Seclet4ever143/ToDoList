<template>
  <v-col cols="12" md="8" class="d-flex flex-column">
    <v-card class="pa-4 flex-grow-1" color="secondary">
      <v-card-title class="headline">Add To List</v-card-title>
      <v-divider></v-divider>
      <v-form>
        <v-text-field 
          v-model="localTaskTitle" 
          label="Task Title" 
          dense
          hide-details
          @input="updateTaskTitle"
        />
        <v-textarea 
          v-model="localTaskDetails" 
          label="Task Details" 
          dense
          hide-details
          rows="3"
          @input="updateTaskDetails"
        />
        <v-select 
          v-model="localCategory" 
          :items="categories" 
          label="Category" 
          dense
          hide-details
          @change="updateCategory"
        />
        <v-btn @click="addTask" class="mt-5 ms-5 bg">Add to List</v-btn>
      </v-form>
    </v-card>
  </v-col>
</template>

<script>
export default {
  props: ['categories', 'newTaskTitle', 'newTaskDetails', 'selectedCategory'],
  data() {
    return {
      localTaskTitle: this.newTaskTitle,
      localTaskDetails: this.newTaskDetails,
      localCategory: this.selectedCategory,
    };
  },
  methods: {
    updateTaskTitle() {
      this.$emit('update:newTaskTitle', this.localTaskTitle);
    },
    updateTaskDetails() {
      this.$emit('update:newTaskDetails', this.localTaskDetails);
    },
    updateCategory() {
      this.$emit('update:selectedCategory', this.localCategory);
    },
    addTask() {
      this.$emit('add-task');
    }
  },
  watch: {
    newTaskTitle(newVal) {
      this.localTaskTitle = newVal;
    },
    newTaskDetails(newVal) {
      this.localTaskDetails = newVal;
    },
    selectedCategory(newVal) {
      this.localCategory = newVal;
    }
  }
};
</script>

<style scoped>
.bg {
  background-color: rgba(0, 255, 255, 0.573);
  border-radius: 5px;
}
</style>
