<template>
  <nb-container :style="{backgroundColor: '#fff'}">
    <AppNavigationEvents :onDidFocus="checkForMessage" />
    <nb-header>
      <nb-body>
        <nb-title>Login</nb-title>
      </nb-body>
    </nb-header>
    <nb-content padder>
      <nb-form>
        <InputWithError
          :error="$v.form.email.$dirty && !$v.form.email.required"
          msg="Email is required"
        >
          <nb-input
            v-model="form.email"
            placeholder="Email"
            auto-capitalize="none"
            :on-blur="() => $v.form.email.$touch()"
          />
        </InputWithError>

        <InputWithError
          :error="$v.form.password.$dirty && !$v.form.password.required"
          msg="Password is required"
        >
          <nb-input
            v-model="form.password"
            placeholder="Password"
            auto-capitalize="none"
            secure-text-entry
            :on-blur="() => $v.form.password.$touch()"
          />
        </InputWithError>
      </nb-form>
      <view :style="{marginTop:10}">
        <nb-button :on-press="login" block>
          <nb-text>Login</nb-text>
        </nb-button>
      </view>
      <nb-button transparent :on-press="goToRegister">
        <nb-text>Not Registered ? Register here</nb-text>
      </nb-button>
      <nb-button transparent :on-press="goToHome">
        <nb-text>Navigate to Home</nb-text>
      </nb-button>
    </nb-content>
  </nb-container>
</template>


<script>
import { required } from "vuelidate/lib/validators";
import { Toast } from "native-base";
// import { AsyncStorage } from 'react-native'

export default {
  props: {
    navigation: {
      type: Object
    }
  },
  data() {
    return {
      form: {
        email: "",
        password: ""
      }
    };
  },
  validations: {
    form: {
      email: {
        required
      },
      password: {
        required
      }
    }
  },

  // This is redirect the user to the homeScreen when successfully logged in
  async created() {
    // getters can get the state
    // auth/isAuth : gets the state 
    const isAuth = this.$store.getters['auth/isAuth']

    if (isAuth) { this.navigation.navigate("Home") }
   
  },

  methods: {
    login() {
      this.$v.form.$touch();

      if (!this.$v.form.$invalid) {
        this.$store
          .dispatch("auth/login", this.form)
          .then(user => {
            this.navigation.navigate("Home");
          })
          .catch(err => {
            Toast.show({
              text: "Wrong email or password!",
              buttonText: "Okay",
              type: "danger",
              duration: 3000
            });
          });
      }
    },
    checkForMessage() {
      const message = this.navigation.getParam('message');
      if (message) {
        Toast.show({
          text: message,
          buttonText: "Okay",
          type: "success",
          duration: 3000
        });
      }
    },
    goToRegister() {
      this.navigation.navigate("Register");
    },
    goToHome() {
      this.navigation.navigate("Home");
    }
  }
};
</script>

