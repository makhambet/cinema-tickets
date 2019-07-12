<template>
  <div class="about">
    <tickets ></tickets>
  </div>
</template>

<script>
// @ is an alias to /src
import tickets from '@/components/HelloWorld.vue'

export default {
  name: 'home',
  data() {
    return {
      ticket: []
    }
  },
  components: {
    tickets
  }
}
</script>
