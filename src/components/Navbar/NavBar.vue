<template>
  <b-navbar toggleable="lg" type="dark" variant="light">
  <b-container>
      <b-navbar-brand href="#">
        <img src="@/assets/transfer-logo.png" alt="Logo" class="flex align-start" />
        Transfer bank
      </b-navbar-brand>
    </b-container>
  </b-navbar>
</template>

<script>
export default {
  name: 'NavBar'
};
</script>

<style scoped>
img {
  width: 45px;
  height: 45px;
  margin-left: 15%;
}
</style>