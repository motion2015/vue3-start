<template lang="">
  <List :items="todos">
    <template #default="{item, index}">
      <div 
      class="card-body p-2 d-flex align-items-center"
      @click="moveToPage(item.id)"
      style="cursor:pointer"
      >
        <div class="flex-grow-1">
          <input 
          type="checkbox" 
          class="mr-2 ml-2"
          :checked="item.completed" 
          @change="toggleTodo(index, $event)"
          @click.stop
          />
          <span 
          :class="{todo: item.completed}"
          >{{item.subject}}</span>
        </div>
        <div>
          <button class="btn btn-danger btn-sm" @click.stop="openModal(item.id)">Delete</button>
        </div>
        </div>
      </template>
  </List>
  <teleport to="#modal">
    <Modal v-if="showModal" @close="closeModal" @delete="deleteTodo" />
  </teleport>
  
</template>
<script>
import { useRouter } from 'vue-router';
// import { watchEffect } from '@vue/runtime-core';
import Modal from '@/components/DeleteModal'
import List from '@/components/List'
import { ref } from 'vue';
export default {
  props: {
    todos: {
      type: Array,
      required: true
    }
  },
  components: {
    Modal,
    List
  },
  emits : ['toggle-todo','delete-todo'],
  setup(props, {emit}) {
    const router = useRouter()
    const showModal = ref(false)
    const todoDeleteId = ref(null)
    const toggleTodo = (index, event) => {      
      emit('toggle-todo', index, event.target.checked);
    }

    const openModal = (id) => {
      console.log('openModal');
      todoDeleteId.value= id
      showModal.value = true
    }
    const closeModal = () => {
      console.log('openModal');
      todoDeleteId.value= ''
      showModal.value = false
    }
    const deleteTodo = () => {
      console.log('deleteTodo',todoDeleteId.value);
      emit('delete-todo', todoDeleteId.value);
      showModal.value= false
      todoDeleteId.value= null
    }
    const moveToPage = (todoId) => {      
      // router.push('/todos/'+ todoId);
      router.push({
        name: 'Todo',
        params: {
          id: todoId
        }
      });
      console.log(todoId, 'dfdfdf');
    }
    return {
      toggleTodo,
      deleteTodo,
      moveToPage,
      showModal,
      openModal,
      closeModal
    }
  }
}
</script>
<style scoped>
.mr-2{margin-right:0.5rem!important;}
.ml-2{margin-left:0.5rem!important;}
</style>