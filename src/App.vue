<template>
  <div>
    <LoadingView v-if="showLoading"/>
    <MainHeader/>
    <RouterView />
  </div>
</template>

<script>
import { RouterLink, RouterView } from 'vue-router';
import MainHeader from './components/MainHeader.vue';
import LoadingView from './components/LoadingView.vue';

export default {
  components: {
    RouterLink,
    RouterView,
    MainHeader,
    LoadingView
  },
  data() {
    return {
      showLoading: true,
      reloadCount: 0
    };
  },
  mounted() {
    const storedCount = sessionStorage.getItem('reloadCount');
    if (storedCount) {
      this.reloadCount = JSON.parse(storedCount);
    }

    window.addEventListener('beforeunload', this.reloadPage);

    if (this.reloadCount <= 2) {
      setTimeout(() => {
        this.showLoading = false;
      }, 2000);
    }else {
      this.showLoading = false;
    }
  },
  beforeDestroy() {
    window.removeEventListener('beforeunload', this.reloadPage);
  },
  methods: {
    reloadPage() {
      this.reloadCount++;
      sessionStorage.setItem('reloadCount', JSON.stringify(this.reloadCount));
    }
  }
}
</script>
