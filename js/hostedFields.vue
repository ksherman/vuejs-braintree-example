<template>
  <div class="constrain">
    <form id="hostedFieldsPayment" @submit.prevent="formHandler('post', '/the/route')">
      <h2>Hosted Fields</h2>
      <div id="error-message"></div>

      <label for="card-number">Card Number</label>
      <div class="hosted-field" id="card-number"></div>

      <label for="cvv">CVV</label>
      <div class="hosted-field" id="cvv"></div>

      <label for="expiration-date">Expiration Date</label>
      <div class="hosted-field" id="expiration-date"></div>

      <input type="hidden" name="payment-method-nonce" id="paymentMethodNonce">
      <label>How much monies?</label>
      <input type="text" name="checkoutValue" class="hosted-field" placeholder="$10">

      <input type="submit" value="SEND THE MONIES" style="padding-top: 1rem;" disabled id="submitTransaction">
    </form>
  </div>
</template>

<script>
export default {
  props: [
    'token'
  ],
  created() {
    this.btHF();
  },
  data () {
    return {
      clientToken: this.token
    }
  },
  methods: {
    formHandler(action, route) {

      console.log("Action: " + action + " | Route: " + route);
    },
    btHF() {
      var client = require('braintree-web/client');
      var hostedFields = require('braintree-web/hosted-fields');
      
      client.create({
        authorization: this.clientToken
      }, function (clientErr, clientInstance) {
        if (clientErr) {
          // Handle error in client creation
          return;
        }

        hostedFields.create({
          client: clientInstance,
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
            console.error(hostedFieldsErr);
            return;
          }
          //enable submit button
          document.querySelector('#submitTransaction').removeAttribute('disabled');
          
          // add event listener on the submit button. this listens for a submission action e.g. clicking the submit button.
          document.querySelector('#hostedFieldsPayment').addEventListener('submit', function (event) {
            // prevents the form from being submitted to tokenize payment
            event.preventDefault();

            // tokenize the payment information to generate a nonce, returns an error to print to the console or a payload
            hostedFieldsInstance.tokenize( function (tokenizeErr, payload) {
              if (tokenizeErr) {
                console.error(tokenizeErr);
                return;
              }

              // print payload object to the console. probably would be removed in production. if you enable 'Preserve log' in Chrome, you can see the payload even after a page load.
              console.log(payload);
              // inject nonce value into the hidden field
              document.querySelector('#paymentMethodNonce').value = payload.nonce;

            });
          }, false);

        });
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

  label {
    font-size: 16px;
    display: block;
    text-align: left;
    font-weight: bold;
  }

  .hosted-field {
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