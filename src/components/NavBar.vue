<template>
  <div>
    <nav>
      <div class="nav-wrapper">
        <h3 style="margin-left: 2%; margin-top: 1%" class="left">PETS</h3>
        <!-- <ul id="nav-mobile" class="left show-on-med-and-down">
          <li><a href="#">Mascotas</a></li>
          <li><a href="#">Categorias</a></li>
        </ul> -->
      </div>
    </nav>
  </div>
</template>

<script>
export default {
  data: () => ({
    activeItem: 0,
  }),
};
</script>

<style scoped>
header,
main,
footer {
  padding-left: 300px;
}

@media only screen and (max-width: 992px) {
  header,
  main,
  footer {
    padding-left: 0;
  }
}
</style>