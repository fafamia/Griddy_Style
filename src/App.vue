<template>
  <div>
    <MainHeader/>
    <RouterView />
  </div>
</template>

<script>
import { RouterLink, RouterView } from 'vue-router';
import MainHeader from './components/MainHeader.vue';

export default {
  components: {
    RouterLink,
    RouterView,
    MainHeader
  }
}
</script>
