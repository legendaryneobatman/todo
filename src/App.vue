<script>

const FILTERS_MAP = {
  all: {label: 'Все', filterHandler: () => true},
  active: {label: 'Активные', filterHandler: (el) => el.active},
  completed: {label: 'Завершенные', filterHandler: (el) => !el.active},
}
export default {
  name: 'App',
  data() {
    return {
      todoList: [],
      activeFilter: FILTERS_MAP.all,
      search: null,
      filters: FILTERS_MAP,
      newTodoText: '',
    };
  },
  computed: {
    filteredTodoList() {
      return this.todoList.filter(todo =>
          this.activeFilter.filterHandler(todo) &&
          (!this.search || todo.text.includes(this.search))
      )
    },
  },
  mounted() {
    if (this.search) this.search.focus();
  },
  async created () {
    this.todoList =  (await (
        await fetch("https://my-json-server.typicode.com/falk20/demo/todos")
    ).json())
  },
  methods: {
    createTodo() {
      this.todoList.push({
        id: Date.now().toString(),
        text: this.newTodoText,
        active: true,
      });

      this.newTodoText = '';
    },
    onRemoveTodo(item) {
      this.todoList = this.todoList.filter(({id}) => id !== item.id)
    },
    onTodoComplete (item) {
      this.todoList = this.todoList.map((el) => item.id === el.id ? {...el, active: !el.active} : el)
    },
    setActiveFilter(filterKey) {
      this.activeFilter = this.filters[filterKey]
    },
  }
}
</script>

<template>
  <div class="container mx-auto">
    <div class="flex gap-4 mb-8">
      <label class="flex flex-col gap-2">
        Поиск
        <input ref="search" v-model="search"/>
      </label>
      <label class="flex flex-col gap-2">
        Действия
        <input placeholder="Текс задачи" ref="search" v-model="newTodoText"/>
        <button class="h-fit" @click="createTodo">Добавить задачу</button>
      </label>
      <label class="flex flex-col gap-2">
        Фильтры
        <button
            v-for="(filter, filterKey) in filters" :key="filterKey"
            @click="setActiveFilter(filterKey)"
            :class="filter.label === activeFilter.label && 'border-2 border-solid'"
        >
          {{ filter.label }}
        </button>
      </label>
    </div>

    <div class="flex flex-col gap-4">
      <div
          class="flex items-center gap-4 relative rounded border-gray-500 border-solid border-2 p-4"
          :class="[!item.active && 'opacity-25']"
          v-for="item in filteredTodoList"
          :key="item.id"
      >
        <div v-if="!item.active" class="absolute after:content-[``] border-b-[1px] border-black border-solid w-full left-0 top-1/2 -translate-y-1/2 pointer-events-none"/>
        <input :checked="!item.active" type="checkbox" @change="onTodoComplete(item)">
        {{ item.text }}
        <button class="ml-auto" v-on:click="onRemoveTodo(item)">delete</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>
