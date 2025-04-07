<template>
  <div class="home">
    <HelloWorld msg="Progressor (compo API) " />
    <Form @createtask="addTask" />
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from "@/components/HelloWorld.vue";
import Form from "@/components/Form.vue";
import tasksService from '@/services/tasks.js'

export default {
  name: "HomeView",
  components: {
    HelloWorld,
    Form,
  },
  setup() {
    function addTask(data) {
      console.log('Home.vue | addTask()', data);
      tasksService.create(data);
    }

    return { addTask };
  }
};
</script>
