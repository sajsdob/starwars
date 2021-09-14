<template>
<nav class="navbar container" role="navigation" aria-label="main navigation">
  <div class="navbar-brand">
    <a class="text-decoration-none" href="/">
      <strong class="is-size-4">S B-B rec task</strong>
    </a>
  </div>
  <div id="navbar" class="">
    <div class="navbar-end">
      <div class="navbar-item">
        <div class="buttons">
          <!-- Check that the SDK client is not currently loading before accessing is methods -->
          <div v-if="!$auth.loading">
            <!-- show login when not authenticated -->
            <a v-if="!$auth.isAuthenticated" @click="login" class="btn btn-dark text-light"><strong>Sign in</strong></a>
            <!-- show logout when authenticated -->
            <a v-if="$auth.isAuthenticated" @click="logout" class="btn btn-dark text-light"><strong>Log out</strong></a>
          </div>
        </div>
      </div>
    </div>
  </div>
</nav>
</template>
<script>
export default {
  name: 'Nav',
  methods: {
  // Log the user in
  login() {
    this.$auth.loginWithRedirect();
  },
  // Log the user out
  logout() {
    this.$auth.logout({
      returnTo: window.location.origin
    });
  }
  }
}
</script>
<style lang="scss" scoped>
  nav {
    margin-top: 25px;
    margin-bottom: 30px;
    a {
      font-weight: bold;
      color: #2c3e50;
      &.router-link-exact-active {
        color: #d88d00;
      }
    }  
  } 
</style>