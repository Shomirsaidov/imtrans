<template>
  <div class="sticky sticky-top position-sticky top-0 bg-white py-3">
    <nav>
      <router-link to="/">Шомирсаидов Абубакр</router-link> |
      <router-link to="/about">2024</router-link>
    </nav>
    <div>
      <h4 class="mb-8 text-xl font-bold">Прототипи барнома барои озмуни Илм Фуруғи маърифат</h4>
    </div>
  </div>
  <router-view/>
</template>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
