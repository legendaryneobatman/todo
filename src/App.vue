<script>

const FILTERS_MAP = {
  all: {label: 'Все', value: 'id'},
  active: {label: 'Активные', value: 'active'},
  completed: {label: 'Завершенные', value: 'completed'},
}
export default {
  data() {
    return {
      todo: [],
      activeFilter: FILTERS_MAP.all,
      search: null,
      filters: FILTERS_MAP,
      newTodoText: '',
    };
  },
  computed: {
    filteredTodos() {
      return this.todo
          .filter(todo => todo[this.activeFilter.value])
          .filter(({text}) => {
            if (!this.search) return true;
            return text.includes(this.search);
          })
    },
  },
  mounted() {
    if (this.search) {
      this.search.focus();
    }
    fetch("https://my-json-server.typicode.com/falk20/demo/todos")
        .then((response) => {
          return response.json();
        })
        .then((data) => {
          this.todo = data.map(el => ({
            ...el,
            completed: false,
          }))
        })
  },
  methods: {
    createTodo() {
      const newTodo = {
        id: Date.now(),
        text: this.newTodoText,
        active: false,
      };
      this.todo.push(newTodo);

      this.newTodoText = '';
    },
    remove(todo) {
      this.todo = [...this.todo.filter(({id}) => id !== todo.id)]
    },
    changeTodoField (todo, key, value) {
      this.todo = [...this.todo].map(el => {
        if (el.id === todo.id) {
          return {
            ...el,
            [key]: value
          }
        }
        return el;
      });
    },
    toggleTodoField (todo, key) {
      this.todo = [...this.todo].map(el => {
        return {
          ...el,
          [key]: el.id === todo.id
        };
      });
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
        <button v-for="(filter, filterKey) in filters" :key="filterKey" @click="setActiveFilter(filterKey)">{{ filter.label }}</button>
      </label>
    </div>

    <div class="flex flex-col gap-4">
      <div
          class="rounded border-gray-500 border-solid border-2 p-4"
          :class="item.active && 'border-red-500'"
          v-for="item in filteredTodos"
          :key="item.id"
          @click="toggleTodoField(item, 'active')"
      >
        <input :value="item.completed" type="checkbox" @change="changeTodoField(item, 'completed', !Boolean(item.completed))">
        {{ item.text }}
        <button v-on:click="remove(item)">delete</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>
