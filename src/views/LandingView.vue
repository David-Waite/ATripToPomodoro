<script setup>
import {
  signInWithEmailAndPassword,
  createUserWithEmailAndPassword,
  getAuth,
  onAuthStateChanged,
} from "firebase/auth";
import db from "@/main";
import { doc, setDoc } from "firebase/firestore";
</script>

<template>
  <div class="container">
    <h1>Login</h1>

    <form @submit.prevent="loginUser">
      <div>
        <label for="email">email</label>
        <input
          v-model="email"
          type="text"
          placeholder="Email"
          required
          id="email"
        />
        <p
          class="errorMsg"
          v-if="
            errorMessage === `Invalid email address` ||
            errorMessage === `No account with that email found`
          "
        >
          <i class="bi bi-exclamation-circle-fill"></i>{{ errorMessage }}
        </p>
      </div>
      <div>
        <label for="password">Password</label>
        <input
          v-model="password"
          type="password"
          placeholder="Password"
          required
          id="password"
        />
        <p v-if="errorMessage === `Password was incorrect`" class="errorMsg">
          <i class="bi bi-exclamation-circle-fill"></i>{{ errorMessage }}
        </p>
      </div>

      <button class="button-19" type="submit">LOGIN</button>
    </form>
  </div>
  <div class="container">
    <img
      class="background"
      src="../assets/imageBackface.png"
      alt="background image "
    />
    <h1>A Trip to <br /><span>Pomodoro</span></h1>
    <form @submit.prevent="registerUser">
      <label for="email">email</label>
      <input
        v-model="email"
        type="email"
        placeholder="Email"
        required
        id="email"
      />
      <label for="username">username</label>
      <input
        v-model="username"
        type="text"
        placeholder="Username"
        required
        id="username"
      />
      <label for="password">password</label>
      <input
        v-model="password"
        type="password"
        placeholder="Password"
        required
        id="password"
      />
      <button type="submit">Sign Up</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      email: "",
      password: "",
      errorMessage: "",
    };
  },
  methods: {
    async registerUser() {
      try {
        console.log(this.email, this.password);
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
            1,
          ],
        });

        // Other user properties}
        this.$router.push("/");
      } catch (error) {
        console.error("Error creating user:", error);
      }
    },
    async loginUser() {
      try {
        console.log(this.email, this.password);
        // Use Firebase authentication API to create a new user
        await signInWithEmailAndPassword(getAuth(), this.email, this.password);
        // Redirect to the home page or another route
        this.$router.push("/");
      } catch (error) {
        console.log(error.code);
        switch (error.code) {
          case "auth/user-not-found":
            this.errorMessage = "No account with that email found";
            break;
          case "auth/invalid-email":
            this.errorMessage = "Invalid email address";
            break;
          case "auth/invalid-credential":
            this.errorMessage = "Password was incorrect";
            break;
          default:
            this.errorMessage = "Something went wrong";
            break;
        }

        console.error("Error creating user:", error);
      }
    },
  },
  mounted() {
    this.auth = getAuth();
    onAuthStateChanged(this.auth, (user) => {
      if (user) {
        this.$router.push("/");
        return;
      }
    });
  },
};
</script>
