<template>
  <v-app>
    <v-app-bar>
      <v-container class="d-flex align-center py-0">
        <v-app-bar-title class="pl-0">
          <div class="d-flex align-center">Ribbon</div>
        </v-app-bar-title>
      </v-container>
    </v-app-bar>

    <v-main>
      <section id="hero">
        <v-sheet class="d-flex align-center pb-16" color="grey-darken-3">
          <v-container class="text-center">
            <v-responsive class="mx-auto">
              <h3 class="text-h3">Try Ribbon's all new features</h3>

              <p class="mt-4 text-medium-emphasis">
                Our all-in-one platform gives you the banking, accounting,
                fundraising, and organizational tools you need to build a
                successful charity under the umbrella of your fiscal sponsor.
              </p>
            </v-responsive>
          </v-container>
        </v-sheet>
      </section>

      <v-sheet>
        <section id="filter">
          <v-container>
            <v-row justify="space-between">
              <v-col cols="auto">
                <h2 class="text-h4">Ribbon Donor List</h2>

                <p class="text-primary mt-3">In Beta now!</p>

                <p class="mt-3">See all those that have given in one place!</p>
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <table v-if="donors">
                  <thead>
                    <tr>
                      <th class="text-left">Name</th>
                      <th class="text-left">Email</th>
                      <th class="text-left">Total Donations</th>
                      <th class="text-left">First Donation</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="item in donors.data" :key="item.id">
                      <td>{{ item.full_name }}</td>
                      <td>{{ item.email }}</td>
                      <td>{{ item.total_donations }}</td>
                      <td>{{ item.first_donation }}</td>
                    </tr>
                  </tbody>
                </table>
              </v-col>
            </v-row>
          </v-container>
        </section>
      </v-sheet>

      <v-sheet class="py-16" color="#1818181a">
        <section id="grid">
          <v-container>
            <v-row justify="space-between">
              <v-col cols="auto">
                <v-responsive width="350">
                  <h2 class="text-h4">Show your support feature</h2>
                  <p class="text-success mt-3">Available now!</p>
                  <p class="mt-3">
                    Easily send messages to those that have given!
                  </p>
                </v-responsive>
              </v-col>
              <v-sheet width="400" class="mx-auto">
                <v-form
                  ref="form"
                  v-model="valid"
                  validate-on="submit"
                  @submit.prevent="sendMessage"
                >
                <v-text-field
                    v-model="selectedEmail"
                    :rules="emailRules"
                    label="Email"
                    outlined
                    clearable
                    @change="updateSelectedDonor"
                  >
                  <template #prepend-inner>
                  <v-icon color="grey lighten-1">mdi-account-search</v-icon>
                </template>
              </v-text-field>
                  <v-textarea
                    v-model="message"
                    :rules="messageRules"
                    label="Message"
                    outlined
                    counter
                  ></v-textarea>
                 
                  <v-text-field
                    v-model="selectedDonorId"
                    label="Donor Id"
                    outlined
                    counter
                    disabled
                  ></v-text-field>
                  <v-btn type="submit" :disabled="!valid" color="primary">Send Message</v-btn>
                </v-form>
              </v-sheet>
            </v-row>
          </v-container>
        </section>
      </v-sheet>
    </v-main>

    <v-footer>
      <v-container
        class="text-overline d-flex align-center justify-space-between"
      >
        <div>Copyright &copy; 2023 Flourish Change Inc dba Ribbon</div>

        <v-icon icon="mdi-bank" size="x-large" />
      </v-container>
    </v-footer>
  </v-app>
</template>

<script>
import axios from "axios";
export default {
  name: "App",

  data() {
    return {
      donors: [],
      valid: false,
      email: "",
      donor_id: "",
      message: "",
      selectedDonorId: null,
      selectedEmail: "",
      emailRules: [
        (v) => !!v || "Email is required",
        (v) =>
          /.+@.+\..+/.test(v) || "Email must be valid", 
      ],
      messageRules: [
        (v) => !!v || "Message is required",
        (v) =>
          v && v.length >= 15 || "Message must be at least 15 characters",
      ],
    };
  },
  mounted() {
    axios
      .get("https://interview.ribbon.giving/api/donors")
      .then((response) => (this.donors = response.data));
  },
  methods: {
    updateSelectedDonor() {
      if (this.selectedEmail) {
        const donor = this.donors.data.find(
          (donor) => donor.email === this.selectedEmail
        );
        if (donor) {
          this.selectedDonorId = donor.id;
        }
      }
    },
    sendMessage() {
      if(!this.selectedDonorId){
        alert("Email is not belong to any donor");
        return;
      }
      axios
        .post(
          `https://interview.ribbon.giving/api/donors/${this.selectedDonorId}/send-message`,
          {
            message: this.message,
          },
          {
            headers: {
              Accept: "application/json",
              "Content-Type": "application/json",
            },
          }
        )
        .then(() => {
          this.$refs.form.reset();
          this.selectedDonorId = null;
          this.selectedEmail = "";
          this.message = "";
          alert('Message sent successfully!');
        })
        .catch((error) => {
        alert('An error occurred while sending the message.');
          console.error(error);
        });
    },
    async submit() {
      // Send message to server.
    },
  },
};
</script>
