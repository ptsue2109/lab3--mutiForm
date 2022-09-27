<template>
  <div id="app">
    {{ currentStep }}
    <ContainerApp />
  </div>
</template>

<script>
import ContainerApp from "./view/Container.vue";
import { mapState } from "vuex";
export default {
  name: "App",
  components: {
    ContainerApp,
  },
  data: function() {
    return {
    }
  },
  computed: {
    ...mapState(['currentStep'])
  }
}
</script>

<style scoped>
</style>
