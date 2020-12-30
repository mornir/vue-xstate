<template>
  <p>
    Result: <strong>{{ state.context.result }}</strong>
  </p>
  <p>
    State: <strong>{{ state.value }}</strong>
  </p>
  <input
    type="text"
    @input="send({ type: 'TYPE', data: $event.target.value })"
    :value="state.context.searchText"
  />

  <ul>
    <li v-for="item in state.context.items" :key="item">{{ item }}</li>
  </ul>
</template>

<script>
import { assign, createMachine, send, actions } from "xstate"
import { useMachine } from "@xstate/vue"
import { computed, ref } from "vue"

const { cancel } = actions

const DELAY = 1000

const searchMachine = createMachine(
  {
    id: "search",
    initial: "idle",
    context: {
      searchText: "",
      result: undefined,
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
        result: (_, event) => event.data,
      }),
      cancelSearchEvent: cancel("searchEvent"),
    },
    services: {
      async search() {
        return new Promise((resolve, reject) => {
          setTimeout(() => {
            resolve(Math.random())
          }, 1000)
        })
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
