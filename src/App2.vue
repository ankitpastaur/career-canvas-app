<script setup>
//This is using composition API

import { onMounted, ref } from "vue";


    const name = ref("Ankit Pastaur");
    const status = ref("pending");
    const tasks = ref(["task 1", "task2", "task3"]);

    const toggleStatus = () => {
      if (status.value === "active") {
        status.value = "pending";
      } else if (status.value === "pending") {
        status.value = "inactive";
      } else {
        status.value = "active";
      }
    };

    const addTask = () => {
      if(newTask.value.trim() !==''){
        tasks.value.push(newTask.value);
        newTask.value = '';
      }
    }

    const deleteTask = (index) => {
      tasks.value.splice(index,1)
    }
    onMounted(
      async () =>{
        try {
          const response = await fetch('https://jsonplaceholder.typicode.com/todos');
          const data = await response.json();
          tasks.value = data.map((task)  => task.title);
        } catch (error) {
          console.log('Error in fetching data');

        }
      }
    );
</script>

<template>
  <h1>{{ name }}</h1>
  <br />
  <p v-if="status === 'active'">User is active</p>
  <p v-else-if="status === 'pending'">User is pending</p>
  <p v-else>User is inactive</p>

  <form @submit.prevent="addTask">
    <label for="newTask">Add Task</label>
    <input type="text" name="newTask" id="newTask" v-model="newTask" />
    <button type="submit">Submit</button>
  </form>

  <h3>Tasks:</h3>

  <ul>
    <li v-for="(task, index) in tasks" key="task">
      <span>{{ task }}
      </span>
      <button @click="deleteTask(index)">X</button>
      
    </li>
  </ul>

  <button v-on:click="toggleStatus">Change Status</button>
</template>
