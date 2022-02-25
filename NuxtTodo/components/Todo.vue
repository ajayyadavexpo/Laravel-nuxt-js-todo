<!-- Please remove this file from your project -->
<template>
  <div class="relative flex items-top justify-center min-h-screen bg-gray-100 sm:items-center sm:pt-0">
    <div class="w-full">
      <!-- component -->
      <div class="h-100 w-full flex items-center justify-center bg-teal-lightest font-sans">
        <div class="bg-white rounded shadow p-6 m-4 w-full lg:w-3/4 lg:max-w-lg">
            <div class="mb-4">
                <h1 class="font-bold text-3xl text-gray-600 text-center">Todo List</h1>
                <div class="flex mt-4">

                    <input class="shadow appearance-none border rounded w-full py-2 px-3 mr-4 text-grey-darker" placeholder="Add Todo" v-model="content">

                    <button class="flex-no-shrink p-2 border-2 rounded-lg text-teal border-teal text-white bg-blue-500 hover:bg-blue-700" @click="submit">Add</button>
                </div>
            </div>
            <div>
                <div class="flex mb-4 items-center" v-for="(todo,i) in todos" :key="todo.id">

                    <p :class="[todo.is_done ? 'line-through text-green-600' : '', `w-full text-grey-darkest font-semibold text-gray-600`]">{{ todo.content }}</p>

                    <button v-if="todo.is_done" class="flex-no-shrink w-1/3 p-2 ml-4 mr-2 border-2 rounded-lg border-grey" @click="updateStatus(todo,i)">Not Done</button>

                    <button v-else class="flex-no-shrink p-2 ml-4 mr-2 border-2 rounded-lg border-green text-white bg-green-500 hover:bg-green-700" @click="updateStatus(todo,i)">Done</button>


                    <button class="flex-no-shrink p-2 ml-2 border-2 rounded-lg text-red border-red text-white bg-red-500 hover:bg-red-700" @click="deleteTodo(todo,i)">Remove</button>
                </div>
            </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
export default {
  name: 'NuxtTutorial',
  data(){
    return{
      content:'',
      todos:[],
    }
  },
  methods:{
    submit:function(){
      this.$nuxt.$loading.start();
      this.$axios.post(`${this.$axios.defaults.baseURL}todos`,{content:this.content}).then(res=>{
        this.todos.unshift(res.data);
        this.content = '';
      }).catch(error=>{
        console.log(error);
      }).finally(()=>{
        // loader stop
        this.$nuxt.$loading.finish();
      });
    },

    updateStatus:function(todo,i){
      this.$nuxt.$loading.start();
      let api = `${this.$axios.defaults.baseURL}todos/`+todo.id;
      this.$axios.put(api).then(res=>{
        this.todos[i].is_done = res.data.is_done;
      }).catch(error=>{
        console.log(error);
      }).finally(()=>{
        // loader stop
        this.$nuxt.$loading.finish();
      });
    },

    deleteTodo:function(todo,i){
      this.$nuxt.$loading.start();
      let api = `${this.$axios.defaults.baseURL}todos/`+todo.id;
      this.$axios.delete(api).then(res=>{
        if(res.data == true){
          this.todos.splice(i,1)
        }
      }).catch(error=>{
        console.log(error);
      }).finally(()=>{
        // loader stop
        this.$nuxt.$loading.finish();
      });
    },
    

  },
  async fetch(){
    this.todos = await fetch(`${this.$axios.defaults.baseURL}todos`).then(res=>res.json())
  }
}
</script>
