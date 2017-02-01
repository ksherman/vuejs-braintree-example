<template>
  <div class="constrain">
    <div style="text-align: center;">
      <button class="paypal" @click="paypalTxn" disabled>Pay with PayPal</button>
      <button class="card" @click="paymentMethod = 'card'">Pay with Card</button>
    </div>

    <div v-show="this.paymentMethod == 'card'">
      <h2>About Me</h2>
      <div class="line">
        <label for="billing-first-name">First Name
          <input type="text" class="input-field" name="billing-first-name" v-model="$parent.txn.billing.firstName">
        </label>
        <label for="billing-last-name">Last Name
          <input type="text" class="input-field" name="billing-last-name" v-model="$parent.txn.billing.lastName">
        </label>
      </div>

      <label for="customer-email">Email Address
        <input type="text" class="input-field" name="customer-email" v-model="$parent.txn.customer.email">
      </label>

      <label for="customer-phone">Phone Number
        <input type="text" class="input-field" name="customer-phone" v-model="$parent.txn.customer.phone">
      </label>
      
      <label for="billing-street-address">Street Address
        <input type="text" class="input-field" name="billing-street-address" v-model="$parent.txn.billing.streetAddress">
      </label>
      
      <label for="billing-extended-address">Extended Address
        <input type="text" class="input-field" name="billing-extended-address" v-model="$parent.txn.billing.extendedAddress">
      </label>
      
      <label for="billing-city">City
        <input type="text" class="input-field" name="billing-city" v-model="$parent.txn.billing.city">
      </label>
      
      <div class="line">
        <label for="billing-state">State
          <input type="text" class="input-field" name="billing-state" v-model="$parent.txn.billing.state">
        </label>
        <label for="billing-postal-code">Zip
          <input type="text" class="input-field" name="billing-postal-code" v-model="$parent.txn.billing.postalCode">
        </label>
      </div>

      <h2>Payment Information</h2>
      <div v-if="errorMessage">{{ this.errorMessage }}</div>

      <template v-if="showPaymentFields">
        <label for="card-number">Card Number
          <div class="input-field" id="number"></div>
        </label>

        <div class="line">
        <label for="cvv">CVV
          <div class="input-field" id="cvv"></div>
        </label>

        <label for="expiration-date">Expiration Date
          <div class="input-field" id="expiration-date"></div>
        </label>
        </div>

        <button type="submit" style="padding-top: 1rem;" disabled id="submitTransaction" @click="tokenizeHF">TOKENIZE</button>
      </template>
      <template v-else>
        <label for="nonce">Nonce</label>
        <div class="input-field">{{ $parent.txn.payment.paymentMethodNonce }}</div>
      </template>

    </div>

    <label>How much monies?
      <input type="text" name="checkoutValue" class="input-field" placeholder="$10" v-model="$parent.txn.payment.amount">
    </label>

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
      paypalInstance: '',
      showPaymentFields: 'true',
      paymentMethod: ''
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
      }, (clientErr, clientInstance) => {
        if (clientErr) {
          this.errorMessage = 'There was an error setting up the client! Message: ' + clientErr.message;
        } else {
          this.clientInstance = clientInstance
          this.createHF();
          this.createPP();
        }
      });
      
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
            selector: '#number',
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
      }, (hostedFieldsErr, hostedFieldsInstance) => {
        if (hostedFieldsErr) {
          // Handle error in Hosted Fields creation
          this.errorMessage = 'There was an error setting up the hosted fields! Message: ' + hostedFieldsErr.message;
          return;
        } else {
          //enable submit button
          document.querySelector('#submitTransaction').removeAttribute('disabled');
          this.hostedFieldsInstance = hostedFieldsInstance;
        }

      });
    },
    tokenizeHF() {
      this.hostedFieldsInstance.tokenize( (tokenizeErr, payload) => {
        if (tokenizeErr) {
          this.errorMessage = 'There was an error tokenizing! Message: ' + tokenizeErr.message;
          console.log(tokenizeErr)
          return;
        }

        this.tokenizePayload = payload;
        this.$parent.txn.payment.paymentMethodNonce = payload.nonce;

        // teardown HF and present payment information
        this.hostedFieldsInstance.teardown( (teardownErr) => {
          if (teardownErr) {
            this.errorMessage = 'There was an error tearing it down! Message: ' + teardownErr.message;
          } else {
            this.showPaymentFields = false;
          }
        });

      });
    },
    createPP() {
      var paypal = require('braintree-web/paypal');
      paypal.create({
        client: this.clientInstance
      }, (createPaypalErr, paypalInstance) => {
        if (createPaypalErr) {
          // Handle error in PayPal creation
          this.errorMessage = 'There was an error setting up the hosted fields! Message: ' + createPaypalErr.message;
          return;
        } else {
          //enable submit button
          //document.querySelector('#submitTransaction').removeAttribute('disabled');
          this.paypalInstance = paypalInstance;
          document.querySelector('.paypal').removeAttribute('disabled');
        }
      });
    },
    paypalTxn() {
      this.paymentMethod = 'paypal';
      console.log(this.paypalInstance);

      this.paypalInstance.tokenize({
        flow: 'vault'
      }, (pptokenizeErr, paypalPayload) => {

        if (pptokenizeErr) {
          // Handle tokenization errors or premature flow closure

          switch (pptokenizeErr.code) {
            case 'PAYPAL_POPUP_CLOSED':
              console.error('Customer closed PayPal popup.');
              break;
            case 'PAYPAL_ACCOUNT_TOKENIZATION_FAILED':
              console.error('PayPal tokenization failed. See details:', pptokenizeErr.details);
              break;
            case 'PAYPAL_FLOW_FAILED':
              console.error('Unable to initialize PayPal flow. Are your options correct?', pptokenizeErr.details);
              break;
            default:
              this.errorMessage = 'There was an error tokenizing PayPal! Message: ' + pptokenizeErr.message;
              console.error('Error!', pptokenizeErr);
          }
        } else {
          
          this.$parent.txn.payment.nonce = paypalPayload.paymentMethodNonce;
          this.$parent.txn.payment.paypal = paypalPayload;

        }

      });


    }
  }
}
</script>

<style>
  button.paypal {
    background-color: #00457C;
    font-weight: 600;
  }

  button.paypal:disabled {
    background-color: #E6E6E6;
  }

  button.card {
    background-color: black;
    color: #44a948;
    font-weight: 600;
  }

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
    padding: 0.25rem;
  }

  .input-field {
    height: 50px;
    box-sizing: border-box;
    width: 100%;
    padding: 0.8rem;
    display: inline-block;
    box-shadow: none;
    font-weight: 600;
    font-size: 0.8rem;
    border-radius: 6px;
    border: 1px solid #dddddd;
    line-height: 1.2;
    background: #fcfcfc;
    margin-top: 0.1rem;
    margin-bottom: 0.8rem;
    background: linear-gradient(to right, white 50%, #fcfcfc 50%);
    background-size: 200% 100%;
    background-position: right bottom;
    transition: all 300ms ease-in-out;
    font-size: 18px;
    text-align: left;
  }
</style>