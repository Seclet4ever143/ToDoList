<template>
  <v-app>
    <v-container fluid>
      <v-row>
        <v-col cols="12">
          <ToDoList/>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import ToDoList from './components/ToDoList.vue';

export default {
  components: {
    ToDoList
  }
};
</script>
