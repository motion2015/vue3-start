<template>
    <div class="d-flex justify-content-between mb-3">
      <h2>To-Do List</h2>
      <button class="btn btn-primary" @click="moveToCreatePage">Create Todo</button>
    </div>
    <input 
    class="form-control" 
    type="text" 
    v-model="searchText" 
    placeholder="search"
    @keyup.enter="searchTodo"
    />
    <hr />
    <!-- <TodoSimpleForm @add-todo="addTodo"/> -->
    <div class="my-2">{{error}}</div>
    <div v-if="!todos.length"  class="my-2">
    There is nothing to display
  </div>
  <TodoList :todos="todos" @toggle-todo="toggleTodo" @delete-todo="deleteTodo" />
  <hr />
  <nav aria-label="Page navigation example">
    <ul class="pagination">
      <li class="page-item" v-if="currentPage !==1"><a style="cursor: pointer;" class="page-link" @click="getTodos(currentPage-1)">Previous</a></li>
      <li  style="cursor: pointer;" class="page-item" :class="{active: currentPage==page}" v-for="(page,idx) in numberOfPage" :key="idx"><a class="page-link" @click="getTodos(page)">{{page}}</a></li>
      <li  style="cursor: pointer;" class="page-item" v-if="numberOfPage != currentPage"><a class="page-link"  @click="getTodos(currentPage+1)">Next</a></li>
    </ul>
  </nav>
  <Toast 
    v-if="showToast"
    :message="toastMessage"
    :type="toastAlertType"
    />
</template>
<script>
import { computed, ref, watch } from 'vue'; 
//import TodoSimpleForm from '@/components/TodoSimpleForm'
import TodoList from '@/components/TodoList'

// import './assets/styles.scss'
import axios from '@/axios'
import { useRouter } from 'vue-router';
import Toast from '@/components/Toast';
import { useToast } from '@/composables/toast'

export default {
  name: 'TodosPage',
  components: {
    //TodoSimpleForm,
    TodoList,
    Toast
  },
  setup() {
    const router = useRouter();
    const todos = ref([]);
    const error = ref('');
    const numberOfTodos = ref(0);
    let limit = 5;
    const currentPage = ref(1)
    const searchText = ref('')


    const numberOfPage = computed(()=> {
      return Math.ceil(numberOfTodos.value/limit)
    })

    const { 
      showToast, 
      toastMessage, 
      toastAlertType, 
      triggerToast, 
    } = useToast()
    
    /* const a= reactive({
      b: 1
    }) */

    const moveToCreatePage = () => {
      router.push({
        name: 'TodoCreate'
      })
    }
    
    let timeout = null;
    const searchTodo = () => {
      clearTimeout(timeout);
      getTodos(1)
    }
    watch(searchText, ()=> {
      clearTimeout(timeout)
      timeout =setTimeout(()=>{
        getTodos(1)
      }, 2000)
    })


    const getTodos = async (page = currentPage.value) => {
      currentPage.value= page
      try {
        const res = await axios.get(`todos?_sort=id&_order=desc&subject_like=${searchText.value}&_page=${page}&_limit=${limit}`)
        numberOfTodos.value = res.headers['x-total-count']
        todos.value= res.data        
      } catch(err) {
        error.value = 'Something went wrong, (' + err + ')'
        triggerToast('Something went wrong', 'danger')
      }
    }
    getTodos();
    const addTodo = async (todo) => { 
      error.value = ''
      //데이터베이스 투두에 저장
      try {
        await axios.post('todos', {
          subject: todo.subject,
          computed: todo.completed
        })
        getTodos(1)
        //todos.value.push(res.data); 
      } catch (err) {
        error.value = 'Something went wrong, (' + err + ')' 
        triggerToast('Something went wrong', 'danger')
      }
      // .then(res => {
      //   ///console.log(res);
      //   todos.value.push(res.data);   
      // }).catch(err => {
      //   error.value = 'Something went wrong, (' + err + ')'
      // })
      
    };
   
    const deleteTodo = async (id) => {
      //console.log('삭제하자', idx);
      error.value = ''
      //const id = todos.value[idx].id; 아이를 찾는 과정이 필요없어짐.
      try {
        await axios.delete('todos/' + id)
        getTodos(1)
        //todos.value.splice(idx, 1);
      } catch(err) {
        error.value = 'Something went wrong, (' + err + ')' 
        triggerToast('Something went wrong', 'danger')
      }      
    };

    const toggleTodo = async (idx , checked) => {
      console.log(checked);
      error.value = ''
      const id = todos.value[idx].id;
      try {
        await axios.patch('todos/' + id, {
          // completed: !todos.value[idx].completed
          completed: checked
        })
        // todos.value[idx].completed = !todos.value[idx].completed
        todos.value[idx].completed = checked
      } catch(err) {
        error.value = 'Something went wrong, (' + err + ')' 
        triggerToast('Something went wrong', 'danger')
      }
      
    }

    
    /* const filteredTodos = computed(() => {
      if(searchText.value) {
        return todos.value.filter(todo => {
          return todo.subject.includes(searchText.value)
        })
      }
      return todos.value
    }) */
    return {
      addTodo,
      todos,
      // todoStyle,
      deleteTodo,
      toggleTodo,
      searchText,
      // filteredTodos,
      error,
      getTodos,
      numberOfPage,
      currentPage,
      searchTodo,
      moveToCreatePage,
      showToast, 
      toastMessage, 
      toastAlertType, 
    };
  }
}
</script>
<style>
.todo {
  color: gray;
  text-decoration: line-through
}
hr{color:#f8f9fa;}
</style>