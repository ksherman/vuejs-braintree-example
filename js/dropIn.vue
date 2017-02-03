<template>
  <div class="constrain">
    <h2>Drop-in UI [<a href="https://github.com/braintree/braintree-web-drop-in" target="_blank">beta</a>]</h2>
    
    <div v-if="errorMessage">{{ this.errorMessage }}</div>

    <div id="dropin-container"></div>


    <div class="line">
      <label>How much monies?
        <input type="text" name="checkoutValue" class="input-field" placeholder="$10" v-model="$parent.txn.payment.amount">
      </label>

      <button type="submit" style="padding-top: 1rem;" disabled id="submitTransaction" @click="dropinSubmit">MONEY!</button>

    </div>

  </div>
</template>

<script>
export default {
  props: [
    'token'
  ],
  created() {
    this.btDropin();
  },
  data () {
    return {
      clientToken: this.token,
      errorMessage: '',
      dropinInstance: '',
      dropinPayload: ''
    }
  },
  methods: {
    btDropin() {
      var dropin = require('../drop-in/dist/web/dropin/1.0.0-beta.4/js/dropin.min.js');
      console.log('Braintree Drop-in Web Beta');
      dropin.create({
        authorization: this.clientToken,
        selector: '#dropin-container',
        paypal: {
          flow: 'vault',
          amount: this.$parent.txn.payment.amount,
          currency: 'USD'
        }
      }, (dropinErr, dropinInstance) => {
        if (dropinErr) {
          this.errorMessage = 'There was an error setting up the Drop-in! Message: ' + dropinErr.message;
          return;

        } else {
          document.querySelector('#submitTransaction').removeAttribute('disabled');
          this.dropinInstance = dropinInstance;
        }
      })
    },
    dropinSubmit() {
      this.dropinInstance.requestPaymentMethod( (dropinPayloadErr, dropinPayload) => {
        if (dropinPayloadErr) {
          this.errorMessage = 'There was an error with Drop-In Payload! Message: ' + dropinPayloadErr.message;
          return;
        } else {
          this.dropinPayload = dropinPayload;
          if (dropinPayload.type.includes('PayPal')) {
            this.$parent.txn.payment.paypal = dropinPayload;
          } else {
            this.$parent.txn.payment.paypal = '';
          }
          this.$parent.txn.payment.paymentMethodNonce = dropinPayload.nonce;
        }
      });
    }

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

  button[type="submit"] {
    margin-top: 1rem;
    background-color: #44a948;
  }
</style>