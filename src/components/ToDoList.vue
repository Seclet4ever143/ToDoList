<template>
    <v-card class="pa-4"  color="secondary">
      <v-card-title class="headline">To-Do-List</v-card-title>
      <v-divider></v-divider>
      
      <v-row v-if="filteredTasks.length === 0" class="text-center">
        <v-col>
          <v-card class="pa-4 bg">
            <v-card-title>No tasks yet</v-card-title>
          </v-card>
        </v-col>
      </v-row>
      <v-row v-else>
        <v-col v-for="task in filteredTasks" :key="task.id" cols="12" md="4">
          <v-card class="pa-4 mb-3 bg">
            <v-card-title>{{ task.title }}</v-card-title>
            <v-card-subtitle>Details:</v-card-subtitle>
            <v-divider></v-divider>
            <v-list class="bg" dense >
              <v-list-item-group>
                <v-list-item v-for="detail in task.details" :key="detail">
                  <v-list-item-content>
                    <v-list-item-title>
                      <v-icon left>mdi-bullet</v-icon>
                      {{ detail }}
                    </v-list-item-title>
                  </v-list-item-content>
                </v-list-item>
              </v-list-item-group>
            </v-list>
            <v-divider></v-divider>
            <v-card-actions>
              <v-btn @click="$emit('edit-task', task)" icon>
                <v-icon>mdi-pencil</v-icon>
              </v-btn>
              <v-btn @click="$emit('toggle-complete', task)" icon>
                <v-icon :color="task.completed ? 'green' : 'black'">mdi-check-circle</v-icon>
              </v-btn>
              <v-btn @click="$emit('delete-task', task)" icon>
                <v-icon>mdi-delete</v-icon>
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
    </v-card>
  </template>
  
  <script>
  export default {
    props: {
      tasks: Array,
      selectedCategory: String
    },
    computed: {
      filteredTasks() {
        if (this.selectedCategory) {
          return this.tasks.filter(task => task.category === this.selectedCategory);
        }
        return this.tasks;
      }
    }
  };
  </script>

  <style>
    .bg{
        background-color: rgba(0, 255, 255, 0.573);
        border-radius: 5px;
    }
</style>