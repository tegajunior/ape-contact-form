<template>
  <v-container fluid class="text-center pt-7">
    <h2>Please fill the form below</h2>
    <v-card width="800" class="mx-auto text-center mt-15">
      <v-card-title  class="primary--text">
        Contact info
      </v-card-title>
      <v-divider></v-divider>
      <v-card-text>
        <v-form ref="contactForm" v-model="formValidity">
          <v-row justify="space-between">
            <v-col cols="12" md="6">    
              <v-text-field
                dense
                name="firstname"
                label="First name"
                v-model.trim="form.firstname"
                type="text"
                required
                :outlined="true"
                :rules="firstNameRules"
              ></v-text-field>
            </v-col>
            <v-col cols="12" md="6">
              <v-text-field
                dense
                name="lastname"
                type="text"
                required
                label="Last name"
                v-model.trim="form.lastname"
                :outlined="true"
                :rules="lastNameRules"
             ></v-text-field>
            </v-col>
            <v-col cols="12" md="6">
              <v-text-field
                dense
                name="email"
                type="email"
                required
                label="Email"
                v-model.trim="form.email"
                :outlined="true"
                :rules="emailRules"
              ></v-text-field>
            </v-col>
            <v-col cols="12" md="6">
              <v-text-field
                dense
                name="phone"
                type="number"
                label="Phone number"
                v-model.trim="form.phonenumber"
                :outlined="true"
                :rules="phoneNumberRules"
              ></v-text-field>
            </v-col>
            <v-col cols="12" md="6">
              <v-select
                dense
                :items="genders"
                v-model="form.gender"
                label="Select gender"
                outlined
              ></v-select>
            </v-col>
            <v-col cols="12" md=6>
              <v-autocomplete
                dense
                :items="countries"
                v-model="form.country"
                label="Select country"
                outlined
              ></v-autocomplete>
            </v-col>
            <v-col cols="12">
              <v-text-field
                type="text"
                v-model.trim="form.address"
                label="Address"
                outlined
                :rules="addressRules"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-card-actions>
            <v-btn color="error" @click="resetForm">Reset</v-btn>
            <v-spacer></v-spacer>
            <v-btn color="success" :disabled="!formValidity" @click="submit">Submit</v-btn>
          </v-card-actions>
        </v-form>
      </v-card-text>
    </v-card>
    <v-dialog
      v-model="dialog"
      scrollable
      persistent
      :hide-overlay="false"
      max-width="500px"
      transition="dialog-top-transition"
    >
      <v-card>
        <v-card-title class="headline success--text lighten-2 mr-2">
          <v-avatar
            size="34"
          >
            <v-icon color="success">mdi-check</v-icon>
          </v-avatar>
          <span>Form Submitted</span>
        </v-card-title>
        <v-card-text>
          <v-list flat>
            <v-list-item-group v-model="selectedItem">
              <v-list-item v-for="(item, index) in userData" :key="index">
                <v-list-item-content>
                  <strong>{{ item.label }}: </strong> <i>{{ item.value }}</i>
                </v-list-item-content>
              </v-list-item>
            </v-list-item-group>
          </v-list>
        </v-card-text>
        <v-divider></v-divider>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="closeDialog">Ok</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
  import axios from "axios"
  export default {
    name: 'Home',
    mounted() {
      this.fetchCountries()
    },
    data: () => ({
      dialog: false,
      selectedItem: "",
      genders: ["Male", "Female", "I don't want to disclose"],
      countries: [],
      form: {
        firstname: "",
        lastname: "",
        phonenumber: "",
        gender: "",
        country: "",
        address: "",
        email: "",
      },
      userData: [
        {
          buffer: "firstname",
          label: "First name",
          value: ""
        },
        {
          buffer: "lastname",
          label: "Last name",
          value: ""
        },
        {
          buffer: "email",
          label: "Email",
          value: ""
        },
        {
          buffer: "phonenumber",
          label: "Phone number",
          value: ""
        },
        {
          buffer: "gender",
          label: "Gender",
          value: ""
        },
        {
          buffer: "country",
          label: "Country",
          value: ""
        },
        {
          buffer: "address",
          label: "Address",
          value: ""
        }
      ],
      firstNameRules: [
        value => !!value || "First name is required",
      ],
      lastNameRules: [
        value => !!value || "Last name is required",
      ],
      addressRules: [
        value => !!value || "Address is required"
      ],
      countryRules: [
        value => !!value || "Please select a country"
      ],
      phoneNumberRules: [
        value => !!value || "Phone number is required",
        value => value.length == 11 || "Expecting 11 digits"
      ],
      emailRules: [
      value => !!value || "Email is required.",
      value => value.indexOf("@") !== 0 || "Email should have a username.",
      value => value.includes("@") || "Email should include an @ symbol.",
      value =>
        value.indexOf(".") - value.indexOf("@") > 1 ||
        "Email should contain a valid domain.",
      value => value.includes(".") || "Email should include a period symbol.",
      value =>
        value.indexOf(".") <= value.length - 3 ||
        "Email should contain a valid domain extension."
    ],
    formValidity: false,
    }),
    components: {
    },
    methods: {
      async fetchCountries() {
        axios.get("https://restcountries.eu/rest/v2/all")
        .then((response) => {
          response.data.forEach(element => {
            this.countries.push(element.name)
          });
        })
        .catch((err) => console.log(err));
      },
      resetForm() {
        this.$refs.contactForm.reset();
      },
      submit() {
        this.userData.forEach(element => {
          element.value = this.form[element.buffer];
        });
        this.dialog = true;
      },
      closeDialog() {
        this.resetForm();
        this.dialog = false;
      }
    },
  }
</script>
