<template>
  <ul class="nav nav-justified">
    <li class="nav-item">
      <img src="../assets/home-icon.png" alt="">
      <router-link to="/" class="nav-link">{{ $t("nav.home") }}</router-link>
    </li>
    <li class="nav-item">
      <img src="../assets/gallery-icon.png" alt="">
      <router-link to="/pets" class="nav-link">{{ $t("nav.petsGallery") }}</router-link>
    </li>
    <li class="nav-item">
      <img src="../assets/heart-icon.png" alt="">
      <router-link to="/help" class="nav-link">{{ $t("nav.transferDetails") }}</router-link>
    </li>
    <li class="nav-item">
      <img src="../assets/schedule-icon.png" alt="">
      <router-link to="/calendar" class="nav-link">{{ $t("nav.schedule") }}</router-link>
    </li>
    <li class="nav-item">
      <img src="../assets/about-icon.png" alt="">
      <a class="nav-link" href="#">{{ $t("nav.aboutUs") }}</a>
    </li>
  </ul>
</template>

<script>
export default {
}
</script>

<style scoped>
.nav{
  margin: 1em auto;
}
.nav-item img {
  max-width: 48px;
}
</style>
