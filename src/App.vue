<template>
  <div id="app">
    <NavBar />
    <TransferList @openModal="showModal = true" />
    <ModalNewTransfer :showModal.sync="showModal" @closeModal="showModal = false" />
  </div>
</template>

<script>
import NavBar from './components/Navbar/NavBar.vue';
import TransferList from './features/TransferList.vue';
import ModalNewTransfer from './features/Transfer/new/ModalNewTransfer.vue';

export default {
  name: 'App',
  components: {
    NavBar,
    TransferList,
    ModalNewTransfer,
  },
  data() {
    return {
      showModal: false
    }
  },
};
</script>

<style>
#app {
  text-align: center;
}
</style>
