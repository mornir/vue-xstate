<template>
  <p>
    State: <strong>{{ state.value }}</strong>
  </p>
  <input
    type="text"
    @input="send({ type: 'TYPE', data: $event.target.value })"
    :value="state.context.searchText"
  />

  <ul>
    <li v-for="movie in state.context.results" :key="movie.id">
      {{ movie.title }}
    </li>
  </ul>
</template>

<script lang="ts">
import { assign, createMachine, send, actions } from "xstate"
import { useMachine } from "@xstate/vue"
import { computed, ref } from "vue"

const { cancel } = actions

const DELAY = 1000

type Movie = {
  title: string
}

type Context = {
  searchText: string
  results: Movie[]
}

type Event = { type: "TYPE"; data: string } | { type: "SEARCH"; data: [] }

const searchMachine = createMachine<Context, Event>(
  {
    id: "search",
    initial: "idle",
    context: {
      searchText: "",
      results: [],
    },
    on: {
      TYPE: {
        actions: ["setSearchText", "cancelSearchEvent", "sendSearchEvent"],
      },
      SEARCH: {
        target: ".searching",
      },
    },
    states: {
      idle: {},
      searching: {
        invoke: {
          src: "search",
          onDone: {
            actions: "setResult",
            target: "idle",
          },
          onError: {
            target: "idle",
          },
        },
      },
    },
  },
  {
    actions: {
      setSearchText: assign({
        searchText: (_, event) => event.data,
      }),
      sendSearchEvent: send(
        { type: "SEARCH" },
        { id: "searchEvent", delay: DELAY }
      ),
      setResult: assign({
        results: (_, event) => event.data,
      }),
      cancelSearchEvent: cancel("searchEvent"),
    },
    services: {
      async search({ searchText }, event): Promise<Movie[]> {
        if (!searchText) return []

        const response = await fetch(
          "https://api.themoviedb.org/3/search/movie?api_key=f06a9a69ee7572f528b4781f591fb0f2&language=en-USpage=1&include_adult=false&query=" +
            searchText
        )
        const data = await response.json()
        return data.results
      },
    },
  }
)

export default {
  name: "Search",
  setup() {
    const { state, send } = useMachine(searchMachine)
    return {
      state,
      send,
    }
  },
}
</script>
