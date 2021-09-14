<template>
<div class="home mb-5">
  <section class="hero is-dark">
   <div class="container">
     <div class="hero-body">
       <h3 v-if="$auth.isAuthenticated" class="is-size-3 has-background-dark">Welcome, {{ $auth.user.name }}!</h3>
     </div>
   </div>
  </section>
  <people-table/>
</div>
</template>
<script>

// import axios from 'axios'
import PeopleTable from "@/components/PeopleTable";

export default {
  name: 'home',
  components: {
    PeopleTable
  },
  methods: {
    // Log the user in
    login() {
      this.$auth.loginWithRedirect();
    },
  }
}

</script>
<style lang="scss" scoped>
  .hero {
    text-align: center;
  }
  .hero-body .title {
    text-shadow: 4px 4px 4px rgba(0, 0, 0, 0.6);
    padding: 40px 0 20px 0;
    font-size: 60px;
  }
  .subtitle {
    text-shadow: 4px 4px 4px rgba(0, 0, 0, 0.7);
    font-size: 30px;
  }
  .button-block {
    text-align: center;
    margin-left: auto;
    margin-right: auto;
    width: 100%;
    position: absolute;
    bottom: -150px;
    .button {
      margin-right: 50px;
      padding-left: 50px;
      padding-right: 50px;
    }
    .welcome {
      width: 400px;
      padding-bottom: 100px;
      margin-left: auto;
      margin-right: auto;
    }
  }
  .is-xl {
    font-size: 1.7rem;
  }
</style>