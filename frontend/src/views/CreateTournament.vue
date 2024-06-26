<script setup>
import { ref } from "vue";
import axiosInstance from "@/api/axiosInstance";
import { useAuthStore } from "@/stores";
import { useRouter } from "vue-router";

const CreateTournamentRequest = ref({
  name: "",
  sport: "",
  tournamentType: ""
});
const errorMessage = ref("");
const sessionExpired = ref(false);
const authStore = useAuthStore();
const router = useRouter();

async function createTournament() {
  try {
    // reset the error message
    clearMessages();

    // send the tournament creation request to the server
    const response = await axiosInstance.post(
      "tournament/create", // the endpoint
      createTournamentRequest.value, // the request body
       { withCredentials: false },
    );

    // redirect to the home page
    await router.push("/");
  } catch (error) {
    if (error.response) {
      // An error response was received from the server
      showErrorMessage(error.response.data.message);
    } else if (error.request) {
      // The request was made but no response was received.
      // For example, a CORS error
      showErrorMessage(
        "Unable to connect to the server. Please try again later.",
      );
    } else {
      // Something else went wrong
      showErrorMessage("An error occurred while processing your request.");
    }
  }
}

function clearMessages() {
  errorMessage.value = "";
  sessionExpired.value = false;
}

function showErrorMessage(message) {
  errorMessage.value = message;
}

function extractUserRoleFromToken(token) {
  const decodedToken = JSON.parse(atob(token.split(".")[1]));
  return decodedToken.role;
}

// Check if the route query parameter "sessionExpired" is present
// If it is, set the sessionExpired flag to true, showing the session expired message
if (router.currentRoute.value.query.sessionExpired) {
  sessionExpired.value = true;
}
</script>

<template>
  <section class="py-4 py-md-5 my-5">
    <div class="container py-md-5">
      <div class="row">
        <div class="col-md-6 text-center">
          <img
            class="img-fluid w-100"
            src="src/assets/img/illustrations/login.svg"
            alt="login-img"
          />
        </div>
        <div class="col-md-5 col-xl-4 text-center text-md-start">
          <h2 class="display-6 fw-bold mb-5">
            <span class="underline pb-1">
              <strong>Login</strong>
            </span>
          </h2>
          <form @submit.prevent="login">
            <div class="mb-3">
              <input
                class="shadow form-control"
                v-model="loginRequest.email"
                required="required"
                type=""
                name="email"
                placeholder="Email"
              />
            </div>
            
			<div class="mb-3">
              <input
                class="shadow form-control"
                v-model="loginRequest.password"
                type="password"
                name="password"
                placeholder="Password"
              />
            </div>
            <div class="mb-5">
              <button class="btn btn-primary shadow" type="submit">
                Log in
              </button>
            </div>
            <div v-if="errorMessage" class="alert alert-danger">
              {{ errorMessage }}
            </div>
            <div v-if="sessionExpired" class="alert alert-warning">
              Your session has expired.
              <br />
              Please log in again.
            </div>
            <p class="text-muted">
              Dont have an account?
              <router-link to="/signup"
                >Sign up
                <img
                  src="src/assets/img/arrow-right.svg"
                  alt="Arrow Right Icon"
                />
              </router-link>
            </p>
            <p class="text-muted">
              Forgot your password?
              <router-link to="/forgotten-password"
                >Yes
                <img
                  src="src/assets/img/arrow-right.svg"
                  alt="Arrow Right Icon"
                />
              </router-link>
            </p>
          </form>
        </div>
      </div>
    </div>
  </section>
</template>
