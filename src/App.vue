<template>
  <div id="app">
    <form @submit.prevent="validateBeforeSubmit" v-if="!formSubmitted">
      <h1>Form Validation</h1>
      <div class="form-group" :class="{ 'has-error': errors.has('name') }">
        <label class="control-label" for="name">Name</label>
        <input
          v-model="name"
          v-validate.initial="name"
          data-rules="required|alpha|min:3|max:15"
          class="form-control"
          type="text"
          placeholder="Full Name"
        />
        <p class="text-danger" v-if="errors.has('name')">
          {{ errors.first("name") }}
        </p>
      </div>
      <div class="form-group" :class="{ 'has-error': errors.has('email') }">
        <label class="control-label" for="email">Email</label>
        <input
          v-model="email"
          v-validate.initial="email"
          data-rules="required|email"
          class="form-control"
          type="email"
          placeholder="Email"
        />
        <p class="text-danger" v-if="errors.has('email')">
          {{ errors.first("email") }}
        </p>
      </div>

      <div class="form-group" :class="{ 'has-error': errors.has('secret') }">
        <label class="control-label" for="secret">Password</label>
        <input
          v-model="secret"
          v-validate.initial="secret"
          data-rules="required|passphrase"
          class="form-control"
          type="text"
          placeholder="Enter your password"
        />
        <p class="text-danger" v-if="errors.has('secret')">
          {{ errors.first("secret") }}
        </p>
      </div>

      <div class="form-group" :class="{ 'has-error': errors.has('dropdown') }">
        <label class="control-label">Chooose FOOD Item</label>
        <select v-model="dropdown" v-validate.initial="dropdown">
          <option value="" disabled>Select an option</option>
          <option value="chicken">Chicken Fry</option>
          <option value="mutton">Mutton kary</option>
          <option value="kachi">Kachi</option>
        </select>
      </div>
      <div class="form-group" :class="{ 'has-error': errors.has('checkbox') }">
        <label class="control-label">Does you terms and condition?</label>
        <input
          v-model="checkbox"
          v-validate.initial="checkbox"
          type="checkbox"
        />
      </div>
      <button class="btn btn-primary btn-block" type="submit">Submit</button>
      <button class="btn btn-secondary" type="button" @click="resetForm">
        Reset
      </button>
    </form>
    <div v-else>
      <h1 class="submitted">Form submitted successfully!</h1>
      <div v-if="response">
        <h2 class="text-center">Your Data Will be</h2>
        <h1>Name: {{ response.data.name }}</h1>
        <h1>email: {{ response.data.email }}</h1>
        <h1>dropdown:{{ response.data.dropdown }}</h1>
        <h1>checkbox:{{ response.data.checkbox }}</h1>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from "vue";
import VeeValidate from "vee-validate";

Vue.use(VeeValidate);

VeeValidate.Validator.extend("passphrase", {
  getMessage: (field) =>
    "Password must be at least 8 characters and include uppercase, lowercase, numbers, and special characters..",
  validate: (value) =>
    /^(?=.*\d)(?=.*[a-z])(?=.*[A-Z])(?=.*[!@#$%^&*])\S{8,}$/.test(value),
});

export default {
  data() {
    return {
      email: "",
      name: "",
      url: "",
      secret: "",
      formSubmitted: false,
      response: null,
    };
  },
  methods: {
    validateBeforeSubmit(e) {
      this.$validator.validateAll();

      if (!this.errors.any()) {
        console.log(e);
        this.submitForm();
      }
    },
    resetForm() {
      this.email = "";
      this.name = "";
      this.secret = "";
      this.checkbox = false;
      this.dropdown = "";
      this.formSubmitted = false;
      this.errors.clear();
      this.response = null;
    },
    async submitForm() {
      const mainData = {
        name: this.name,
        data: {
          email: this.email,
          name: this.name,
          secret: this.secret,
          checkbox: this.checkbox,
          dropdown: this.dropdown,
        },
      };
      console.log(this);
      try {
        const response = await fetch("https://api.restful-api.dev/objects", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(mainData),
        });

        if (response.ok) {
          const responseData = await response.json();
          this.response = responseData;
          this.formSubmitted = true;
          console.log(responseData);
          this.submissionResult = "success";
        } else {
          this.submissionResult = "error";
          this.response = "Error: " + response.statusText;
        }
      } catch (error) {
        this.submissionResult = "error";
        this.response = "Fetch Error: " + error;
      }
    },
  },
};
</script>

<style>
body {
  font-family: Helvetica, sans-serif;
}
.container {
  width: 500px;
}
h1 {
  text-align: center;
}
img {
  text-align: center;
}
.submitted {
  color: #4fc08d;
}
</style>
