<template>
  <div class="w-full flex justify-center">
    <input
      type="text"
      placeholder="Enter Pokemon here"
      class="mt-10 p-2 border-blue-500 border-2"
      v-model="text"
    />
  </div>
  <div class="mt-10 p-4 flex flex-wrap justify-center">
    <div
      class="ml-4 text-2xl text-blue-400"
      v-for="(pokemon, idx) in filteredPokemon"
      :key="idx"
    >
      <router-link :to="`/about/${urlIdLookup[pokemon.name]}`">
        {{ pokemon.name }}
      </router-link>
    </div>
  </div>
</template>

<script>
  import { reactive, toRefs } from "@vue/reactivity";
  import { computed } from "@vue/runtime-core";
  export default {
    name: "Home",
    setup() {
      const state = reactive({
        pokemons: [],
        urlIdLookup: {},
        text: "",
        filteredPokemon: computed(() => updatePokemon()),
      });

      const updatePokemon = () => {
        if (!state.text) return [];
        return state.pokemons.filter((pokemon) => {
          return pokemon.name.includes(state.text);
        });
      };

      const fetchPokemons = async () => {
        const res = await fetch("https://pokeapi.co/api/v2/pokemon?offset=0");
        const data = await res.json();
        state.pokemons = data.results;
        state.urlIdLookup = data.results.reduce((acc, cur, idx) => {
          return (acc = { ...acc, [cur.name]: idx + 1 });
        }, {});
      };
      fetchPokemons();
      return { ...toRefs(state) };
    },
  };
</script>
