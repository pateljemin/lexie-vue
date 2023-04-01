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
          <v-col cols="auto">
            <v-text-field
              v-model="search"
              label="Search"
              append-icon="mdi-magnify"
              single-line
            ></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-data-table
              :headers="headers"
              :items="filteredDonors"
              :loading="loading"
              :sort-by.sync="sortBy"
              :sort-desc.sync="sortDesc"
              :items-per-page="5"
              class="custom-table"
            >
            <v-progress-linear v-show="progressBar" slot="progress" color="#00754A" indeterminate></v-progress-linear>
              <template v-slot:item.name="{ item }">
                <v-avatar class="mr-2" size="36">
                  <v-icon>mdi-account-circle</v-icon>
                </v-avatar>
                <span>{{ item.full_name }}</span>
              </template>
              <template v-slot:item.first_donation="{ item }">
                {{ item.first_donation }}
              </template>
              <template v-slot:no-data>
                <v-alert :value="true" color="error" icon="mdi-alert-circle-outline">
                  No data available
                </v-alert>
              </template>
              <template>
                <v-row>
                  <v-col v-if="donors && donors.meta" cols="12">
                    <v-pagination
                      v-model="page"
                      :length="donors.meta.last_page"
                      :total-visible="9"
                      :disabled="loading"
                      color="ribbon"
                    ></v-pagination>
                  </v-col>
                </v-row>
              </template>
            </v-data-table>
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
                  v-model="valid"
                  validate-on="submit"
                  @submit.prevent="submit"
                >
                  <v-textarea
                    v-model="message"
                    :rules="messageRules"
                    label="Message"
                  ></v-textarea>
                  <v-text-field
                    v-model="email"
                    :rules="emailRules"
                    label="Email"
                  ></v-text-field>
                  <v-text-field
                    v-model="donor_id"
                    label="Donor Id"
                  ></v-text-field>
                  <v-btn type="submit" block class="mt-2">Send</v-btn>
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
      progressBar: true,
      loading: true,
      sortBy: "full_name",
      sortDesc: false,
      search: "",
      page: 1,
      headers: [
        {
          text: "Name",
          align: "left",
          sortable: true,
          value: "full_name",
        },
        {
          text: "Email",
          align: "left",
          sortable: true,
          value: "email",
        },
        {
          text: "Total Donations",
          align: "left",
          sortable: true,
          value: "total_donations",
        },
        {
          text: "First Donation",
          align: "left",
          sortable: true,
          value: "first_donation",
        },
      ],
      valid: false,
      email: "",
      donor_id: "",
      message: "",
      emailRules: [
        (value) => {
          if (value) return true;

          return "E-mail is required.";
        },
      ],
      messageRules: [
        (value) => {
          if (value) return true;

          return "Message is required.";
        },
      ],
      watch: {
        donors() {
          this.progressBar = false
        }    
      }
    };
  },
  computed: {
    filteredDonors() {
      if (this.donors) {
        return this.donors.filter((donor) =>
          donor.full_name.toLowerCase().includes(this.search.toLowerCase()) || donor.email.toLowerCase().includes(this.search.toLowerCase())
        );
      }
      return [];
    },
  },
  mounted() {
    this.loading = true;
    axios
      .get("https://interview.ribbon.giving/api/donors")
      .then((response) => {
        this.donors = response.data.data;
        this.loading = false;
      });
  },
  methods: {
    async submit() {
      // Send message to server.
    },
  },
};
</script>

<style>
.custom-table {
  font-size: 16px;
  color: #3A3A40;
}

.custom-table th {
  background-color: rgba(58, 58, 64, 0.05);
}

.custom-table tbody tr:hover {
  background-color: rgba(58, 58, 64, 0.03);
}

</style>