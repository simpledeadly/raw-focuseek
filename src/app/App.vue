<script setup lang="ts">
import type { Todo } from '@/entities/todos/model/types'
import { TodoItem } from '@/shared/components'
import { computed, ref } from 'vue'

const newTodo = ref<string>('')
const hideCompleted = ref<boolean>(false)
const todos = ref<Todo[]>([])

const filteredTodos = computed(() => {
  return hideCompleted.value ? todos.value.filter((t) => !t.isDone) : todos.value
})

function addTodo() {
  if (newTodo.value.trim()) {
    const todo: Todo = {
      id: Date.now(),
      description: newTodo.value,
      isDone: false,
      isEditting: false
    }
    todos.value.push(todo)
  }
  newTodo.value = ''
}

function toggleComplete(id: number) {
  const todo = todos.value.find((t) => t.id === id)
  todo && (todo.isDone = !todo.isDone)
}

function updateTodo(updatedTodo: Todo) {
  const index = todos.value.findIndex((t) => t.id === updatedTodo.id)
  index !== -1 && (todos.value[index] = updatedTodo)
}

function removeTodo(id: number) {
  todos.value = todos.value.filter((t) => t.id !== id)
}
</script>

<template>
  <div class="container">
    <form @submit.prevent class="form">
      <input
        placeholder="Enter todo"
        @keyup.enter="addTodo"
        v-model="newTodo"
        class="input"
        type="text"
      />
      <button @click="addTodo" class="button">Add</button>
    </form>
    <!-- <hr class="divider" /> -->
    <TransitionGroup name="list" tag="ul" class="list">
      <div v-if="filteredTodos.length > 0">
        <TodoItem
          v-for="todo in filteredTodos"
          :key="todo.id"
          :todo="todo"
          @complete="toggleComplete"
          @update="updateTodo"
          @remove="removeTodo"
        />
      </div>
      <div v-else>
        <span>No todos.</span>
      </div>
    </TransitionGroup>
    <button @click="hideCompleted = !hideCompleted" class="button">
      {{ hideCompleted ? 'Show all' : 'Hide checked' }}
    </button>
  </div>
</template>

<style>
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
  font-family: 'Avenir', Arial, Helvetica, sans-serif;
  outline: none;
  color: #ccc;
}

body {
  background: #141414;
}

.container {
  text-align: center;
  margin-top: 5rem;
}

.input {
  margin-bottom: 10px;
  padding: 0.25rem;
  background: #1c1c1c;
  border: 1px solid #090909;
}

.button {
  padding: 0.25rem;
  background: #0e0e0e;
  border: 1px solid #090909;
  border-left: none;
  cursor: pointer;
}

.list {
  list-style-type: none;
  padding: 0;
  margin-bottom: 1rem;
}

.divider {
  border: 0.5px solid #292929;
  width: 50vw;
  margin: 0 auto;
}

.list-enter-active,
.list-leave-active {
  transition: all 0.5s;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}

input[type='checkbox'] {
  appearance: none;
  -webkit-appearance: none;
  -moz-appearance: none;
  background-color: #1c1c1c;
  border: 1px solid #090909;
  width: 16px;
  height: 16px;
  cursor: pointer;
  position: relative;
  margin-left: 0.25rem;
}

input[type='checkbox']:checked::after {
  content: '';
  display: block;
  width: 5px;
  height: 15px;
  border: solid #ccc;
  border-width: 0 2px 2px 0;
  position: absolute;
  top: -5px;
  left: 7px;
  transform: rotate(45deg);
}
</style>
