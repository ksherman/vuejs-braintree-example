<template>
  <div class="constrain">
    <h2>About Me</h2>
    <label for="billing-first-name">First Name</label>
    <input type="text" class="input-field" name="billing-first-name" v-model="$parent.txn.billing.firstName">
    <label for="billing-last-name">Last Name</label>
    <input type="text" class="input-field" name="billing-last-name" v-model="$parent.txn.billing.lastName">
    <label for="customer-email">Email Address</label>
    <input type="text" class="input-field" name="customer-email" v-model="$parent.txn.customer.email">
    <label for="customer-phone">Phone Number</label>
    <input type="text" class="input-field" name="customer-phone" v-model="$parent.txn.customer.phone">
    <label for="billing-street-address">Street Address</label>
    <input type="text" class="input-field" name="billing-street-address" v-model="$parent.txn.billing.streetAddress">
    <label for="billing-extended-address">Extended Address</label>
    <input type="text" class="input-field" name="billing-extended-address" v-model="$parent.txn.billing.extendedAddress">
    <label for="billing-city">City</label>
    <input type="text" class="input-field" name="billing-city" v-model="$parent.txn.billing.city">
    <label for="billing-state">State</label>
    <input type="text" class="input-field" name="billing-state" v-model="$parent.txn.billing.state">
    <label for="billing-postal-code">Zip</label>
    <input type="text" class="input-field" name="billing-postal-code" v-model="$parent.txn.billing.postalCode">

    <h2>Payment Information</h2>
    <div v-if="errorMessage">{{ this.errorMessage }}</div>

    <template v-if="showPaymentFields">
      <label for="card-number">Card Number</label>
      <div class="input-field" id="card-number"></div>

      <label for="cvv">CVV</label>
      <div class="input-field" id="cvv"></div>

      <label for="expiration-date">Expiration Date</label>
      <div class="input-field" id="expiration-date"></div>

      <button type="submit" style="padding-top: 1rem;" disabled id="submitTransaction" @click="tokenizeHF">TOKENIZE</button>
    </template>
    <template v-else>
      <label for="nonce">Nonce</label>
      <div class="input-field">{{ $parent.txn.payment.paymentMethodNonce }}</div>
    </template>

    <label>How much monies?</label>
    <input type="text" name="checkoutValue" class="input-field" placeholder="$10" v-model="$parent.txn.payment.amount">

  </div>
</template>

<script>
export default {
  props: [
    'token'
  ],
  created() {
    this.createBT();
  },
  data () {
    return {
      clientToken: this.token,
      errorMessage: '',
      clientInstance: '',
      tokenizePayload: '',
      hostedFieldsInstance: '',
      showPaymentFields: 'true'
    }
  },
  ready() {
    this.createBT();
  },
  methods: {
    formHandler(action, route) {

      console.log("Action: " + action + " | Route: " + route);
    },
    createBT() {
      var client = require('braintree-web/client');
      client.create({
        authorization: this.clientToken
      }, function (clientErr, clientInstance) {
        if (clientErr) {
          this.errorMessage = 'There was an error setting up the client!' + clientErr;
        } else {
          this.clientInstance = clientInstance
          this.createHF()
        }
      }.bind(this));
      
    },
    createHF() {
      var hostedFields = require('braintree-web/hosted-fields');
      hostedFields.create({
        client: this.clientInstance,
        styles: {
          'input': {
            'font-size': '18px'
          },
          'input.invalid': {
            'color': 'red'
          },
          'input.valid': {
            'color': 'green'
          }
        },
        fields: {
          number: {
            selector: '#card-number',
            placeholder: '4111 1111 1111 1111'
          },
          cvv: {
            selector: '#cvv',
            placeholder: '123'
          },
          expirationDate: {
            selector: '#expiration-date',
            placeholder: '10/2019'
          }
        }
      }, function (hostedFieldsErr, hostedFieldsInstance) {
        if (hostedFieldsErr) {
          // Handle error in Hosted Fields creation
          this.errorMessage = 'There was an error setting up the hosted fields!' + clientErr;
          return;
        } else {
          //enable submit button
          document.querySelector('#submitTransaction').removeAttribute('disabled');
          this.hostedFieldsInstance = hostedFieldsInstance;
        }

      }.bind(this));
    },
    tokenizeHF() {
      this.hostedFieldsInstance.tokenize( function (tokenizeErr, payload) {
        if (tokenizeErr) {
          this.errorMessage = 'There was an error tokenizing!' + clientErr;
          return;
        }

        this.tokenizePayload = payload;
        this.$parent.txn.payment.paymentMethodNonce = payload.nonce;

        // teardown HF and present payment information
        this.hostedFieldsInstance.teardown(function (teardownErr) {
          if (teardownErr) {
            this.errorMessage = 'There was an error tearing it down!' + teardownErr;
          } else {
            this.showPaymentFields = false;
          }
        }.bind(this));

      }.bind(this));
    },
  }
}
</script>

<style>
  .constrain {
    width: 480px;
    margin: 0 auto;
    border: 1px dashed #ff0000;
    padding: 1rem;
  }

  label {
    font-size: 16px;
    display: block;
    text-align: left;
    font-weight: bold;
  }

  .input-field {
    height: 50px;
    box-sizing: border-box;
    width: 100%;
    padding: 12px;
    display: inline-block;
    box-shadow: none;
    font-weight: 600;
    font-size: 14px;
    border-radius: 6px;
    border: 1px solid #dddddd;
    line-height: 20px;
    background: #fcfcfc;
    margin-bottom: 12px;
    background: linear-gradient(to right, white 50%, #fcfcfc 50%);
    background-size: 200% 100%;
    background-position: right bottom;
    transition: all 300ms ease-in-out;
    font-size: 18px;
    text-align: left;
  }
</style>