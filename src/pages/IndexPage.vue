<template>
  <q-page class="row items-center justify-evenly">
    <MainTableComponent />
  </q-page>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import MainTableComponent from 'src/components/MainTableComponent.vue';

export default defineComponent({
  name: 'IndexPage',

  components: {
    MainTableComponent,
  },

  setup() {
    return {};
  },
});
</script>
