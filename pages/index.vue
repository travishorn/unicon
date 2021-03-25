<template>
  <div class="p-12 container mx-auto">
    <div class="py-12">
      <input class="border border-gray-300 px-4 py-2 w-full rounded focus:outline-none focus:border-blue-600"
             type="search"
             placeholder="Search"
             v-model="query" />
    </div>

    <div class="grid grid-cols-3 sm:grid-cols-5 md:grid-cols-6 lg:grid-cols-10 gap-4">
      <div class="bg-gray-100 rounded p-2 cursor-pointer hover:shadow-lg"
           v-for="icon in filteredIcons"
          :key="icon.id"
          :data-tags="icon.tags">
        <div class="p-4">
          <img :alt="`${icon.name} icon`" :src="`/icons/${icon.svg}`" />
        </div>
        <div class="text-center text-xs" :title="icon.tags">{{ icon.name }}</div>
      </div>
    </div>
  </div>
</template>

<script>
import _ from "lodash";
import Fuse from "fuse.js";
import * as icons from "../assets/icons.json";

export default {
  data(){
    return {
      query: "",
      fuse: null,
      filteredIcons: [],
      icons: icons.default.map(icon => {
        return {
          ...icon,
          svg: icon.svg.slice(9)
        };
      })
    }
  },
  watch: {
    query: _.debounce(function (newQuery) {

      if (newQuery === "") return this.filteredIcons = this.icons;
      return this.filteredIcons = this.fuse.search(newQuery).map(result => result.item);
    }, 200)
  },
  mounted() {
    this.fuse = new Fuse(this.icons, {
      keys: ["name", "tags"],
      findAllMatches: true,
      threshold: 0.2
    });

    this.filteredIcons = this.icons;
  }
}
</script>