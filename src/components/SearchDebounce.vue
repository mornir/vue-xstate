<template>
  <h1>Search</h1>
  <input type="text" v-model="searchText" />
  <ul>
    <li v-for="item in filteredItems" :key="item">{{ item }}</li>
  </ul>
</template>

<script>
import { createMachine } from "xstate"
import { computed, ref } from "vue"

const searchMachine = createMachine({
  id: "search",
  initial: "idle",
  states: {
    idle: {},
    searching: {},
  },
})
export default {
  name: "Search",
  setup() {
    const searchText = ref("")
    const items = ["Max", "Patric", "Sophie", "Peter"]

    const filteredItems = computed(() => {
      return items.filter((item) =>
        item.toLowerCase().startsWith(searchText.value)
      )
    })

    return {
      filteredItems,
      searchText,
    }
  },
}
</script>
