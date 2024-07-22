<script setup></script>
<script>
export default {
  props: {
    deleteAccountOverView: Boolean,
  },
  data() {
    return {};
  },
  methods: {
    toggleSettings() {
      this.$emit("toggleSettings");
    },
    toggleDeletePopup() {
      this.$emit("toggleDeleteAccountOverview");
      this.deletePopUp = !this.deletePopUp;
    },
    async handleDelete() {
      console.log("Deleting accoutn");
      //   const db = getFirestore();
      //   const user = this.auth.currentUser;
      //   const uid = user.uid;

      //   // Get a reference to the user's document in Firestore
      //   const userDoc = doc(db, "users", uid);

      //   try {
      //     // Delete the user's document from Firestore
      //     await deleteDoc(userDoc);
      //     console.log("User document deleted successfully");

      //     // Then delete the user's account
      //     await deleteUser(user);
      //     console.log("User account deleted successfully");
      //   } catch (error) {
      //     console.error("Error while deleting user account or document", error);
      //   }
    },
  },
  computed: {},

  created() {},
  mounted() {},
};
</script>
<template>
  <div class="deletePopup" v-if="deleteAccountOverView">
    <hp class="deleteText">
      Are you sure you want to permanently delete your account?
    </hp>
    <button class="deleteBtn btn" @click="handleDelete">DELETE ACCOUNT</button>
    <button class="signOutBtn btn" @click="toggleDeletePopup">CANCEL</button>
  </div>
</template>

<style scoped>
.deletePopup {
  text-align: center;
  max-width: 300px;

  position: absolute;
  z-index: 4;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: var(--background);
  border: 4px solid var(--backgroundBorder);
  border-radius: 0px;
  padding: 8px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
}

.deleteText {
  color: var(--black);
  font-weight: 500;
}
.btn {
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 180px;
  border-radius: 12px;
  height: 36px;
  font-size: 16px;
  font-weight: 600;
  color: var(--white);
  border: none;
  transition: 0.3s;
}
.signOutBtn {
  width: 100%;
  background-color: var(--grey);
}
.deleteBtn {
  width: 100%;
  background-color: var(--red);
}
</style>
