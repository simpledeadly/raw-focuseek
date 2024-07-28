<script setup lang="ts">
import type { Todo } from '@/entities/todos/model/types'
import { ref } from 'vue'

const props = defineProps<{ todo: Todo }>()
const emit = defineEmits(['complete', 'update', 'remove'])

const edittedText = ref(props.todo.description)

function removeTodo() {
  emit('remove', props.todo.id)
}

function toggleComplete() {
  emit('complete', props.todo.id)
}

function startEditting() {
  props.todo.isEditting = true
}

function saveTodo() {
  props.todo.isEditting = false
  props.todo.description = edittedText.value
  emit('update', { ...props.todo })
}

function cancelEditting() {
  props.todo.isEditting = false
  edittedText.value = props.todo.description
}
</script>

<template>
  <li v-if="!todo.isEditting" class="listElement">
    <div class="block">
      <input v-model="todo.isDone" @change="toggleComplete" type="checkbox" />
      <span style="margin-left: 0.5rem">{{ todo.description }}</span>
    </div>
    <div class="buttons">
      <button @click="startEditting" class="button">Edit</button>
      <button @click="removeTodo" class="button">Delete</button>
    </div>
  </li>
  <li v-else class="listElement">
    <div class="block">
      <input v-model="edittedText" @keyup.enter="saveTodo" type="text" class="input" />
    </div>
    <div class="buttons">
      <button @click="saveTodo" class="button">Save</button>
      <button @click="cancelEditting" class="button">Cancel</button>
    </div>
  </li>
</template>

<style scoped>
.listElement {
  margin-bottom: 0.125rem !important;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.25rem;
  width: 250px;
  background: #191919;
  border: 1px solid #090909;
  margin: 0 auto;
}

.block {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.button {
  margin-left: 5px;
  border: 1px solid #090909;
}

.input {
  background: #1d1d1d;
  padding: 0;
  margin: auto 0;
}

.isDone {
  text-decoration: line-through;
  color: gray;
}
</style>
