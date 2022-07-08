<template>
  <div class="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  data() {
    return {}
  },
  computed: {
   
  },
  mounted() {
 
  },
  methods: {
    
  }
}
</script>


<style lang="scss">
@import '@/assets/scss/main.scss';
@import '@/assets/scss/maps.css';

.app {
  min-height: 100vh;
  background: rgb(34,193,195);
  background: linear-gradient(0deg, rgba(34,193,195,1) 0%, rgba(253,187,45,1) 100%);
  padding: 10vh 2vh;
}
</style>
