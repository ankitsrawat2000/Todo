<template>
    <div>
      <h2>To-Do List</h2>
      <input v-model="newTodo" @keyup.enter="addTodo" placeholder="Add new task" />
      <button onclick="addTodo">Add</button>
      <ul>
        <li v-for="todo in todos" :key="todo.id">
          <input type="checkbox" v-model="todo.completed" @change="updateTodo(todo)" />
          <span :class="{ done: todo.completed }">{{ todo.title }}</span>
          <button @click="deleteTodo(todo.id)">❌</button>
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  import axios from "axios";
  
  export default {
    data() {
      return {
        todos: [],
        newTodo: "",
      };
    },
    methods: {
      async fetchTodos() {
        const res = await axios.get("http://localhost:8080/api/todos");
        this.todos = res.data;
      },
      async addTodo() {
        if (!this.newTodo.trim()) return;
        const res = await axios.post("http://localhost:8080/api/todos", {
          title: this.newTodo,
          completed: false,
        });
        this.todos.push(res.data);
        this.newTodo = "";
      },
      async updateTodo(todo) {
        await axios.put(`http://localhost:8080/api/todos/${todo.id}`, todo);
      },
      async deleteTodo(id) {
        await axios.delete(`http://localhost:8080/api/todos/${id}`);
        this.todos = this.todos.filter(todo => todo.id !== id);
      },
    },
    mounted() {
      this.fetchTodos();
    },
  };
  </script>
  
  <style>
  .done { text-decoration: line-through; }
  </style>
  