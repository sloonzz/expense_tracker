<template>
  <div class="container">
    <div class="form-group" v-if="!loading">
      <form v-if="!this.$store.getters['user/isLoggedIn']">
        <div class="form-group">
          <label for="name">Name</label>
          <input type="text" name="name" v-model="name" class="form-control" placeholder="name">
        </div>
        <div class="form-group">
          <label for="email">Email</label>
          <input
            type="email"
            name="email"
            v-model="email"
            class="form-control"
            placeholder="username"
          >
        </div>
        <div class="form-group">
          <label for="password">Password</label>
          <input
            type="password"
            name="password"
            v-model="password"
            class="form-control"
            placeholder="password"
          >
        </div>
        <div class="form-group">
          <label for="password_confirmation">Confirm Password</label>
          <input
            type="password"
            name="password_confirmation"
            v-model="password_confirmation"
            class="form-control"
            placeholder="confirm password"
          >
        </div>
        <button @click.prevent v-on:click="register" class="btn btn-primary">Submit</button>
      </form>
    </div>
    <pulse-loader id="spinner" :loading="loading"></pulse-loader>
  </div>
</template>

<script>
import PulseLoader from "vue-spinner/src/PulseLoader.vue";
export default {
  components: {
    PulseLoader
  },
  data: function() {
    return {
      name: "",
      email: "",
      password: "",
      password_confirmation: "",
      loading: false
    };
  },
  methods: {
    register() {
      this.loading = true;
      this.$store
        .dispatch("auth/registerUser", {
          name: this.name,
          email: this.email,
          password: this.password,
          password_confirmation: this.password_confirmation
        })
        .then(response => {
          this.$store.commit("auth/messages", [["Successfully registered!"]]);
          this.name = "";
          this.email = "";
          this.password = "";
          this.password_confirmation = "";
          this.loading = false;
        })
        .catch(error => {
          this.loading = false;
        });
    }
  },
  created() {
    this.$store.commit("auth/errors", null);
    this.$store.commit("auth/messages", null);
  },
  beforeRouteEnter(to, from, next) {
    next(vm => {
      vm.$store
        .dispatch("auth/retrieveUser")
        .then(response => {
          if (vm.$store.getters["auth/isLoggedIn"]) {
            next("/");
          } else {
            next();
          }
        })
        .catch(error => {
          next();
        });
    });
  }
};
</script>