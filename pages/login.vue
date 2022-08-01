<template>
  <v-layout align-center="center" justify-center="center">
    <v-flex xs12>
      <v-card class="mx-auto pa-5" max-width="420" outlined>
        <v-form ref="form" class="text-center">
          <img
            class="mb-5"
            src="https://web.mejoraysoluciones.com/wp-content/uploads/2021/05/cropped-logo_blackmejoras.png"
          />
          <v-text-field
            v-model="loginData.username"
            background-color="white"
            outlined
            dense
            label="E-mail or Username"
            type="text"
            prepend-inner-icon="mdi-account"
            hide-details
            class="my-3"
            :rules="validationRules.email"
          ></v-text-field>
          <v-text-field
            v-model="loginData.password"
            background-color="white"
            outlined
            dense
            type="password"
            label="Password"
            prepend-inner-icon="mdi-lock"
            hide-details
            class="my-3"
            :rules="validationRules.password"
          ></v-text-field>
          <div class="d-flex justify-end my-0">
            <NuxtLink to="/register">
            <div class="text-right">
              <p class="mt-1 caption">Don´t have account?</p>
            </div>
            </NuxtLink>
          </div>
          <hr class="my-6" color="#e2e2e2" />
          <v-btn
            class="white--text my-3 caption"
            color="#005b94"
            elevation="0"
            block
            :disabled="submitting"
            @click="userLogin()"
          >
            Login
          </v-btn>
        </v-form>
      </v-card>
    </v-flex>
    <v-snackbar v-model="snackbar" :timeout="timeout" :color="color" outlined>
      {{ text }}
    </v-snackbar>
  </v-layout>
</template>
<script>
// import API from '@/services/API.js'
// import Cookies from 'js-cookie'
export default {
  layout: "login",
  middleware: 'anonymous',
  data() {
    return {
      checkbox: false,
      snackbar: false,
      text: "",
      color: "",
      timeout: 2000,
      submitting: false,
      loginData: {
        username: "",
        password: "",
      },
      validationRules: {
        email: [
          (v) => !!v || "Required.",
          (v) => /.+@.+\..+/.test(v) || "Invalid email",
        ],
        password: [(v) => !!v || "Required."],
      },
    };
  },
  head: {
    title: "Login",
  },
  methods: {
    async userLogin() {
      this.submitting = this.$refs.form.validate();
      if (this.$refs.form.validate()) {
        await this.$auth
          .loginWith("laravelSanctum", {
            data: {
              email: this.loginData.username,
              password: this.loginData.password,
            },
          })
          .then((response) => {
            this.$router.push("/home");
            // const data = this.$auth.user.roles[0].permissions;
            // for (let i = 0; i < data.length; i++) {
            //   this.$store.commit("permissions/add", data[i].name);
            // }
          })
          .catch(() => {
            this.submitting = false
            // this.$notifier.showMessage({
            //   content: "Invalid credentials. Please try again.",
            //   color: "#FF5A5F",
            // });
          });
      }
    },
  },
};
</script>
<style>
.google-btn {
  width: 100%;
  height: 36px;
  background-color: #4385f4;
  border-radius: 5px;
}

.apple-btn {
  width: 100%;
  height: 36px;
  background-color: #474747;
  border-radius: 5px;
}

.google-icon-wrapper {
  position: absolute;
  margin-top: 1px;
  margin-left: 1px;
  width: 40px;
  height: 34px;
  border-radius: 5px 0px 0px 5px;
  background-color: white;
}
.google-icon {
  position: absolute;
  margin-top: 8px;
  margin-left: -8px;
  width: 18px;
  height: 18px;
}
.btn-text {
  display: flex;
  justify-content: center;
  flex-direction: wrap;
  padding-top: 8px;
  color: white;
  font-size: 14px;
  letter-spacing: 0.2px;
  font-weight: 200;
}
.google-btn:hover {
  background-color: #0c51c9;
  box-shadow: 0 0 3px #2855a1;
  cursor: pointer;
}
.google-btn:active {
  background: #2855a1;
}

.apple-btn:hover {
  background-color: #000000;
  box-shadow: 0 0 3px #424242;
  cursor: pointer;
}
.apple-btn:active {
  background: #000000;
}
</style>