<template>
 <Modal v-if="isInEditMode" 
       :task="taskToEdit" 
       @updateTask="updateTask($event)" 
       @cancel="cancelEdit" />

  <input type="text" placeholder="filtrer" v-model="letters" @keyup="filter">
  <div class="radio-filters">
    <label for="all">
      <input type="radio" id="all" value="" v-model="selectedTemporality" />Toutes
    </label>
    <label for="short-term">
      <input type="radio" id="short-term" value="short-term" v-model="selectedTemporality" />Court terme
    </label>
    <label for="medium-term">
      <input type="radio" id="medium-term" value="medium-term" v-model="selectedTemporality" />Moyen terme
    </label>
    <label for="long-term">
      <input type="radio" id="long-term" value="long-term" v-model="selectedTemporality" />Long terme
    </label>
  </div>
  <div v-if="tasks.length > 0">
    <div class="task" v-for="task in tasksFiltered" :key="task.id">
      <h3>{{ task.name }}</h3>
      <h3>{{ task.description }}</h3>
      <h3>Echéance : {{ convertCase(task.temporality) }}</h3>
      <button class="small" @click="() => deleteTask(task.id)">Supprimer</button>
      <button class="small" @click="() => toggle(task)">Modifier</button>
      <br><br>
    </div>    
  </div>  
</template>

<script>
import { ref, watch } from "vue";
import tasksService from '@/services/tasks.js';
import Modal from '../components/Modal.vue';

export default {
    name: "Tasks",
    components: {
      Modal
    },
    setup() {
      const tasks = ref([]);
      const letters = ref("");
      const selectedTemporality = ref("");
      tasks.value = tasksService.read();
      const tasksFiltered = ref([]);
      let isInEditMode = ref(false);
      let taskToEdit = ref(null);
      filter();

      function convertCase(temporality) {
        return tasksService.convertCase(temporality);
      }

      function filter() {
        console.log(letters.value);
        if(letters.value.length === 0) {
          tasksFiltered.value = tasks.value;
        } else {
          tasksFiltered.value = tasks.value.filter(t => 
            t.name.toLocaleLowerCase().includes(letters.value.toLocaleLowerCase()))
        }
        if(selectedTemporality.value !== "") {
          tasksFiltered.value = tasksFiltered.value.filter(t => t.temporality === selectedTemporality.value)
        }
      }

      function toggle(task) {
        console.log('toggle task', task);
        taskToEdit.value = task;
        isInEditMode.value = true;
      }

      function updateTask(task) {
        console.log('updateTask', task);
        tasksService.updateTask(task);
        tasks.value = tasksService.read();
        cancelEdit();
      }

      function cancelEdit() {
        isInEditMode.value = false;
        taskToEdit.value = null;
      }

      function deleteTask(id) {
        tasksService.deleteTask(id);
        tasks.value = tasksService.read();
        filter();
      }

      watch(selectedTemporality, (newValue, oldValue) => {
        console.log('newValue: ', newValue, " |",  "oldValue: ", oldValue);
        if(newValue !== '') {
          filter();
        } else {
          tasksFiltered.value = tasks.value;
          filter();
        }
      })

      return { tasks, 
               tasksFiltered, 
               convertCase, 
               letters, 
               selectedTemporality, 
               filter, 
               toggle, 
               isInEditMode,
               updateTask,
               taskToEdit,
               cancelEdit,
               deleteTask,
       };
    }
}
</script>

<style scoped>
  .task {
    margin: 10px 15px;
    border: 3px solid #42b983;
    border-radius: 5px;
  }
  .radio-filters {
    display: flex;
    justify-content: center;
  }
  .small {
    margin: 5px;
    width: 90px;
    background-color: #42b983;
    border: none;
    color: white;
    padding: 10px 10px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 12px;
  }
</style>
