<template>
    <div>
      <h1>Lista de Tarefas</h1>
  
      <!-- Formulário para adicionar nova tarefa -->
      <form @submit.prevent="addTodo">
        <input v-model="newTodo.title" placeholder="Nova tarefa" required />
        <button type="submit">Adicionar</button>
      </form>
  
      <!-- Lista de tarefas -->
      <ul>
        <li v-for="todo in todos" :key="todo.id">
          <span @click="editTodo(todo)" :style="{ textDecoration: todo.completed ? 'line-through' : 'none' }">
            {{ todo.title }}
          </span>
          <button @click="deleteTodo(todo.id)">Remover</button>
          <button @click="toggleComplete(todo)">{{ todo.completed ? "Desfazer" : "Completar" }}</button>
        </li>
      </ul>
  
      <!-- Formulário para editar tarefa -->
      <div v-if="editingTodo">
        <h2>Editar Tarefa</h2>
        <form @submit.prevent="updateTodo">
          <input v-model="editingTodo.title" required />
          <button type="submit">Salvar</button>
          <button @click="cancelEdit">Cancelar</button>
        </form>
      </div>
    </div>
  </template>
  
  <script>
  import axios from "axios";
  
  export default {
    data() {
      return {
        todos: [], // Lista de tarefas
        newTodo: { title: "", completed: false }, // Nova tarefa
        editingTodo: null, // Tarefa em edição
      };
    },
    mounted() {
      this.fetchTodos();
    },
    methods: {
      // Buscar todas as tarefas
      fetchTodos() {
        axios
          .get("http://127.0.0.1:8000/api/todos/")
          .then((response) => {
            this.todos = response.data;
          })
          .catch((error) => {
            console.error("Erro ao buscar tarefas:", error);
          });
      },
      // Adicionar nova tarefa
      addTodo() {
        axios
          .post("http://127.0.0.1:8000/api/todos/", this.newTodo)
          .then((response) => {
            this.todos.push(response.data); // Adiciona a nova tarefa na lista
            this.newTodo.title = ""; // Limpa o campo
          })
          .catch((error) => {
            console.error("Erro ao adicionar tarefa:", error);
          });
      },
      // Excluir uma tarefa
      deleteTodo(id) {
        axios
          .delete(`http://127.0.0.1:8000/api/todos/${id}/`)
          .then(() => {
            this.todos = this.todos.filter((todo) => todo.id !== id); // Remove da lista localmente
          })
          .catch((error) => {
            console.error("Erro ao remover tarefa:", error);
          });
      },
      // Alternar status de completado
      toggleComplete(todo) {
        axios
          .patch(`http://127.0.0.1:8000/api/todos/${todo.id}/`, {
            completed: !todo.completed,
          })
          .then((response) => {
            todo.completed = response.data.completed; // Atualiza o status localmente
          })
          .catch((error) => {
            console.error("Erro ao atualizar tarefa:", error);
          });
      },
      // Iniciar edição de uma tarefa
      editTodo(todo) {
        this.editingTodo = { ...todo }; // Cria uma cópia para edição
      },
      // Atualizar tarefa editada
      updateTodo() {
        axios
          .put(`http://127.0.0.1:8000/api/todos/${this.editingTodo.id}/`, this.editingTodo)
          .then((response) => {
            const index = this.todos.findIndex((todo) => todo.id === response.data.id);
            this.todos[index] = response.data; // Atualiza a lista localmente
            this.editingTodo = null; // Limpa o formulário de edição
          })
          .catch((error) => {
            console.error("Erro ao atualizar tarefa:", error);
          });
      },
      // Cancelar edição
      cancelEdit() {
        this.editingTodo = null;
      },
    },
  };
  </script>
  