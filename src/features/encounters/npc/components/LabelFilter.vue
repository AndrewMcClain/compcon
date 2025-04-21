<template>
  <v-menu offset-y open-on-hover :close-on-content-click="false">
    <template v-slot:activator="{ on }">
      <v-btn icon v-on="on">
        <v-icon>mdi-filter-menu-outline</v-icon>
      </v-btn>
    </template>
    <v-list>
      <v-subheader class="heading h2 white--text primary py-0 px-2 mt-n3">
        Filter NPCs By
      </v-subheader>
      <v-btn block @click="$emit('filtered', clearFilters())">
        Clear Filters
      </v-btn>
      <v-list-item v-for="l in labels" :key="l" class="flavor-text">
        <v-checkbox
          :id="l"
          :value="l"
          :label="l"
          v-model="selectedFilters"
          @change="$emit('filtered', getFilteredNpcs())"
        ></v-checkbox>
      </v-list-item>
    </v-list>
  </v-menu>
</template>

<script lang="ts">
import Vue, { PropType } from 'vue'
import { Npc } from '@/classes/npc'
export default Vue.extend({
  name: 'label-filter',
  props: {
    npcs: {
      type: Array as PropType<Npc[]>,
      required: true,
    },
  },
  data: () => ({
    selectedFilters: [],
  }),
  computed: {
    labels() {
      let labels = new Set<String>([])
      for (const n in this.npcs) {
        for (const l in this.npcs[n].Labels) {
          labels.add(this.npcs[n].Labels[l])
        }
      }
      return Array.from(labels).sort()
    },
  },
  methods: {
    getFilteredNpcs() {
      if (this.selectedFilters.length === 0) {
        return this.npcs
      } else {
        let filteredNpcs = new Array<Npc>()
        for (let n in this.npcs) {
          let matchFilters: boolean = true
          for (let f in this.selectedFilters) {
            matchFilters = matchFilters && this.npcs[n].Labels.includes(this.selectedFilters[f])
          }
          if (matchFilters) {
            filteredNpcs.push(this.npcs[n])
          }
        }
        return filteredNpcs
      }
    },
    clearFilters() {
      this.selectedFilters = []
      return this.getFilteredNpcs()
    }
  },
})
</script>
