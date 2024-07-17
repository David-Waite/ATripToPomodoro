<script setup>
import db from "@/main";
import { BIconXCircle } from "bootstrap-icons-vue";
import { createUserWithEmailAndPassword, getAuth } from "firebase/auth";
import { doc, setDoc } from "firebase/firestore";
</script>
<script>
export default {
  props: {
    show: Boolean,
  },
  data() {
    return {
      register: true,
      email: "",
      password: "",
      confirmPassword: "",
      username: "",
      errorMessage: "",
    };
  },
  methods: {
    async registerUser() {
      try {
        this.auth = getAuth();
        // Use Firebase authentication API to create a new user
        await createUserWithEmailAndPassword(
          this.auth,
          this.email,
          this.password
        );

        // Redirect to the home page or another route
        const userRef = doc(db, "users", this.auth.currentUser.uid);
        // Example: Add user-specific data
        await setDoc(userRef, {
          username: this.username,
          email: this.email,
          settings: {
            focus: 1500,
            shortRest: 300,
            longRest: 3600,
            focusTilLongRest: 4,
          },
          timeStudying: 0,
          vehiclesOwned: [
            {
              name: "Classic",
              price: 0,
              status: "equipped",
            },
          ],
        });

        // Other user properties}

        this.$emit("refresh");
      } catch (error) {
        console.error("Error creating user:", error);
      }
    },
    async loginUser() {
      this.$emit("login", this.email, this.password);
      // try {
      //   // Use Firebase authentication API to create a new user
      //   await signInWithEmailAndPassword(getAuth(), this.email, this.password);
      //   // Redirect to the home page or another route

      //   this.$emit("refresh");
      // } catch (error) {
      //   console.log(error.code);
      //   switch (error.code) {
      //     case "auth/user-not-found":
      //       this.errorMessage = "No account with that email found";
      //       break;
      //     case "auth/invalid-email":
      //       this.errorMessage = "Invalid email address";
      //       break;
      //     case "auth/invalid-credential":
      //       this.errorMessage = "Password was incorrect";
      //       break;
      //     default:
      //       this.errorMessage = "Something went wrong";
      //       break;
      //   }

      //   console.error("Error creating user:", error);
      // }
    },
  },
  computed: {},
  watch: {},
  created() {},
  mounted() {},
};
</script>
<template>
  <div class="accountContainer" v-if="show">
    <div class="header">
      <h1>Sign in or create an account</h1>

      <BIconXCircle @click="$emit('toggleAccount')" />
    </div>
    <h2 class="headerTagline">Save your customization between sessions</h2>
    <div class="switchModeContainer">
      <div
        class="currentBackground"
        :style="register ? { left: '4px' } : { left: '144px' }"
      ></div>
      <div
        class="register toggleButton"
        :class="register && 'selected'"
        @click="register = true"
      >
        <a href="#">SIGN UP</a>
      </div>
      <div
        class="login toggleButton"
        :class="!register && 'selected'"
        @click="register = false"
      >
        <a href="#">LOGIN</a>
      </div>
    </div>
    <form action="#">
      <div>
        <div v-if="register">
          <label for="email">Username</label>
          <input type="text" name="username" id="username" v-model="username" />
        </div>
        <label for="email">Email</label>
        <input type="email" name="email" id="email" v-model="email" />
      </div>

      <div>
        <label for="password">Password</label>
        <input
          type="password"
          name="password"
          id="password"
          v-model="password"
        />
      </div>
      <div v-if="register">
        <label for="confirmPassword">Confirm Password</label>
        <input
          type="password"
          name="confirmPassword"
          id="confirmPassword"
          v-model="confirmPassword"
        />
      </div>

      <button v-if="register" @click.prevent="registerUser">SIGN UP</button>
      <button v-if="!register" @click.prevent="loginUser">LOGIN</button>
    </form>
  </div>
</template>

<style scoped>
.accountContainer {
  position: absolute;
  width: 640px;

  z-index: 5;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: var(--background);
  border: 4px solid var(--backgroundBorder);
  border-radius: 40px;
  padding: 24px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.header h1 {
  text-align: center;
  font-size: 24px;
  color: var(--textPrimary);
}

.header svg {
  position: absolute;
  right: 40px;
  top: 28px;
  cursor: pointer;
  font-size: 24px;
  font-weight: 500;
  color: var(--textPrimary);
}
.headerTagline {
  color: #c89a7a;
  font-size: 12px;
  text-align: center;
  margin-top: 12px;
}
.switchModeContainer {
  margin: 20px 0 20px;
  position: relative;
  display: flex;
  align-items: center;
  background-color: var(--primary);
  width: 280px;
  height: 40px;
  border-radius: 12px;
  transition: left 4s;
}

.toggleButton {
  position: absolute;
  text-align: center;
  z-index: 2;
  width: 140px;
}
.currentBackground {
  position: absolute;
  background-color: var(--background);
  z-index: 1;
  width: calc(140px - 8px);
  height: 32px;
  border-radius: 8px;
  left: 4px;
  transition: 0.4s ease-in-out;
}
.currentLogin {
  right: 4px;
}
.currentRegister {
  left: 4px;
}

.switchModeContainer a {
  font-size: 16px;
  color: var(--background);
  text-decoration: none;
  font-weight: 600;
  transition: 0.4s ease-in-out;
}
.selected a {
  color: var(--textPrimary);
}
.register {
  left: 0px;
}
.login {
  right: 0px;
}
form {
  margin: auto;
  transition: 1s;
  width: 100%;
}
form div {
  display: flex;
  flex-direction: column;
  gap: 4px;
  margin-bottom: 12px;
}
label,
p {
  font-size: 16px;
  color: var(--textPrimary);
  font-weight: 600;
}
p {
  margin-bottom: 4px;
}
input {
  font-size: 16px;
  padding: 8px;
  border-radius: 12px;
  border: 2px solid var(--backgroundBorder);
  background-color: var(--background);
  color: var(--black);
  font-family: RobotoMono;
  font-weight: 600;
}
input:focus {
  outline-style: none;
  background-color: #f9c7b0;
}
form button {
  margin-top: 24px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;

  width: 100%;
  border-radius: 12px;
  height: 36px;
  font-size: 16px;
  font-weight: 600;
  color: var(--white);
  border: none;
  background-color: var(--green);
}
</style>
