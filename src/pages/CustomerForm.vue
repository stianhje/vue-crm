<template>
  <v-container fluid>
    <v-flex xs12>
      <v-card class="grey lighten-4 elevation-0">
        <v-card-title class="title">
          {{ title }}
          <v-spacer></v-spacer>
          <v-btn fab small dark class="grey mr-2" @click.native="cancel()">
            <v-icon dark="">mdi-close-circle-outline</v-icon>
          </v-btn>
          &nbsp;
          <v-btn fab small dark class="blue" @click.native="save()">
            <v-icon>mdi-content-save-all</v-icon>
          </v-btn>
        </v-card-title>
        <v-card-text v-if="loading !== true">
          <v-container fluid grid-list-sm>
            <v-layout row wrap>
              <v-flex md3 sm12>
                <v-img small max-width="20em" :src="avatar" class="avatar "  :srcset="avatar" />
              </v-flex>
              <v-flex md9 sm12>
                <v-container fluid grid-list-sm>
                  <v-layout row wrap>
                    <v-flex md4 sm12 xs12 class="mx-1 px-0">
                      <v-text-field
                        name="firstName"
                        label="First Name"
                        hint="Last name is required"
                        value="Input text"
                        v-model="customer.firstName"
                        class="input-group--focused"
                        required
                      ></v-text-field>
                    </v-flex>
                    <v-flex md4 sm12 class="mx-1 px-0">
                      <v-text-field
                        name="lastName"
                        label="Last Name"
                        maxlength="10"
                        hint="Last name is required"
                        value="Input text"
                        v-model="customer.lastName"
                        class="input-group--focused"
                        required
                      ></v-text-field>
                    </v-flex>
                    <v-flex md4 sm12 xs12 class="mx-1 px-0">
                      <v-text-field
                        name="email"
                        type="email"
                        label="Email"
                        value="Input text"
                        v-model="customer.email"
                        v-bind:rules="rules.email"
                        class="input-group--focused"
                        required
                      ></v-text-field>
                    </v-flex>
                    <v-flex md4 sm12 xs12 class="mx-1 px-0">
                      <v-text-field
                        name="mobile"
                        type="text"
                        label="Mobile"
                        v-model="customer.mobile"
                        class="input-group--focused"
                        required
                      ></v-text-field>
                    </v-flex>
                    <v-flex md4 sm12 class="mx-1 px-0">
                      <v-text-field
                        name="workphone"
                        type="text"
                        label="Work Phone"
                        v-model="customer.workphone"
                        class="input-group--focused"
                        required
                      ></v-text-field>
                    </v-flex>
                    <v-flex md4 sm12 xs12 class="mx-1 px-0">
                      <v-text-field
                        name="rewards"
                        type="number"
                        label="Rewards"
                        hint="number between 0 and 9999"
                        v-bind:rules="rules.rewards"
                        v-model="customer.rewards"
                        class="input-group--focused"
                        required
                      ></v-text-field>
                    </v-flex>
                    <v-flex md6 sm12 class="mx-1 px-0">
                      <v-switch
                        label="Membership"
                        v-model="customer.membership"
                        light
                      ></v-switch>
                    </v-flex>
                  </v-layout>
                </v-container>
              </v-flex>
            </v-layout>
            <v-snackbar
              v-if="loading === false"
              :right="true"
              :timeout="2000"
              :color="mode"
              v-model="snackbar"
            >
              {{ notice }}
              <v-btn dark text @click.native="closeSnackbar">Close</v-btn>
            </v-snackbar>
          </v-container>
        </v-card-text>
      </v-card>
    </v-flex>
  </v-container>
</template>
<script lang="ts">
import { Component } from "vue-property-decorator";
import Vue from "vue";
import { Customer, Entity } from "@/types";
import { customerModule } from "@/store/modules/customers";
import { appModule } from "@/store/modules/app";
import { isValidEmail, isValidRewards } from "@/utils/app-util";

@Component
export default class CustomerForm extends Vue {
  title = "";
  rules = {
    rewards: [() => isValidRewards(this.customer.rewards)],
    email: [() => isValidEmail(this.customer.email)]
  };

  avatar = '';

  customerAvatar() {
    console.log(`${this.customer.avatar}`);
this.avatar = customerModule.customer.avatar;
    return this.customer.avatar;
  }

  get customer() {
    console.log(customerModule.customer);
    return customerModule.customer;
  }

  get orders() {
    return customerModule.getOrders();
  }

  get pagination() {
    return customerModule.pagination;
  }
  get loading() {
    return appModule.loading;
  }
  get mode() {
    return appModule.mode;
  }
  get snackbar() {
    return appModule.snackbar;
  }
  get notice() {
    return appModule.notice;
  }

  save() {
    customerModule.saveCustomer(this.customer);
  }

  cancel() {
    this.$router.push({ name: "Customers" });
  }

  closeSnackbar() {
    appModule.closeNotice();
  }
  created() {
    customerModule.getCustomerById(this.$route.params.id);
  }
  mounted() {
    if (this.$route.params.id) {
      this.title = "Edit Customer";
      this.customerAvatar();
    } else this.title = "New Customer";
  }
}
</script>
<style scoped>
.avatar {
  border-radius: 50%;
}
</style>