<template>
  <div id="app">
    <h1>VueJS Braintree Integration</h1>

    <h2>About Me</h2>
    <div class="line">
      <label for="billing-first-name">First Name
        <input type="text" class="input-field" name="billing-first-name" v-model="this.txn.billing.firstName">
      </label>
      <label for="billing-last-name">Last Name
        <input type="text" class="input-field" name="billing-last-name" v-model="this.txn.billing.lastName">
      </label>
    </div>

    <label for="customer-email">Email Address
      <input type="text" class="input-field" name="customer-email" v-model="this.txn.customer.email">
    </label>

    <label for="customer-phone">Phone Number
      <input type="text" class="input-field" name="customer-phone" v-model="this.txn.customer.phone">
    </label>
    
    <label for="billing-street-address">Street Address
      <input type="text" class="input-field" name="billing-street-address" v-model="this.txn.billing.streetAddress">
    </label>
    
    <label for="billing-extended-address">Extended Address
      <input type="text" class="input-field" name="billing-extended-address" v-model="this.txn.billing.extendedAddress">
    </label>
    
    <label for="billing-city">City
      <input type="text" class="input-field" name="billing-city" v-model="this.txn.billing.city">
    </label>
    
    <div class="line">
      <label for="billing-state">State
        <input type="text" class="input-field" name="billing-state" v-model="this.txn.billing.state">
      </label>
      <label for="billing-postal-code">Zip
        <input type="text" class="input-field" name="billing-postal-code" v-model="this.txn.billing.postalCode">
      </label>
    </div>

    vue-braintree-hosted-fields:

    <!--<hosted-fields
      wrapperClass="constrain"
      :authToken="authToken"
      v-on:bthferror="btHFError"
      v-on:bthfpayload="btHFPayload"
      :collectCardHolderName="true"
      :collectPostalCode="false"
      :enableDataCollector="true"
    >
    </hosted-fields>-->

    <dropin
      wrapperClass="constrain"
      :authToken="authToken"
      :collectCardHolderName="true"
      :enableDataCollector="true"
      :enablePayPal="true"
    >
    </dropin>

    <button type="submit" style="padding-top: 1rem;" id="submitTransaction" @click="submitTransaction">TOKENIZE</button>

  </div>
</template>

<script>
  import HostedFields from './hostedFields.vue';
  import Dropin from './dropin.vue';

  export default {
    name: 'app',
    components: {
      HostedFields,
      Dropin,
    },
    data () {
      return {
        authToken: 'eyJ2ZXJzaW9uIjoyLCJhdXRob3JpemF0aW9uRmluZ2VycHJpbnQiOiJkOWU4ZjQ3MDkwYjVhZGRhMDNhNjAyMTUzNTdmNDhmYWQ3ZGJhZTYxNWNhMWE1MGU1ZmM2ZGJlNTZjMGZkZWZlfGNyZWF0ZWRfYXQ9MjAxNy0wOC0wOVQwNzowNjoyNi4xNjU5NzU0MzgrMDAwMFx1MDAyNmN1c3RvbWVyX2lkPTg5NTQ0NTUwM1x1MDAyNm1lcmNoYW50X2lkPXFwZG5id25qYnJ3MjdrbWpcdTAwMjZwdWJsaWNfa2V5PTM3cmY1ZmR5M2h2a3Z6NDYiLCJjb25maWdVcmwiOiJodHRwczovL2FwaS5zYW5kYm94LmJyYWludHJlZWdhdGV3YXkuY29tOjQ0My9tZXJjaGFudHMvcXBkbmJ3bmpicncyN2ttai9jbGllbnRfYXBpL3YxL2NvbmZpZ3VyYXRpb24iLCJjaGFsbGVuZ2VzIjpbInBvc3RhbF9jb2RlIl0sImVudmlyb25tZW50Ijoic2FuZGJveCIsImNsaWVudEFwaVVybCI6Imh0dHBzOi8vYXBpLnNhbmRib3guYnJhaW50cmVlZ2F0ZXdheS5jb206NDQzL21lcmNoYW50cy9xcGRuYnduamJydzI3a21qL2NsaWVudF9hcGkiLCJhc3NldHNVcmwiOiJodHRwczovL2Fzc2V0cy5icmFpbnRyZWVnYXRld2F5LmNvbSIsImF1dGhVcmwiOiJodHRwczovL2F1dGgudmVubW8uc2FuZGJveC5icmFpbnRyZWVnYXRld2F5LmNvbSIsImFuYWx5dGljcyI6eyJ1cmwiOiJodHRwczovL2NsaWVudC1hbmFseXRpY3Muc2FuZGJveC5icmFpbnRyZWVnYXRld2F5LmNvbS9xcGRuYnduamJydzI3a21qIn0sInRocmVlRFNlY3VyZUVuYWJsZWQiOnRydWUsInBheXBhbEVuYWJsZWQiOnRydWUsInBheXBhbCI6eyJkaXNwbGF5TmFtZSI6IldoaWxkIENhdWdodCIsImNsaWVudElkIjoiQVRsYnV5blItQkg4b0F0c0ZiTFZTdXk4YUpMSjQ4cF9hVXBMNVQyY1dRdXAxRk80bV90RlFEdjM4QXpPQjNEbFBFc1FGOFVGQkF4SC1BclYiLCJwcml2YWN5VXJsIjoiaHR0cDovL2V4YW1wbGUuY29tL3BwIiwidXNlckFncmVlbWVudFVybCI6Imh0dHA6Ly9leGFtcGxlLmNvbS90b3MiLCJiYXNlVXJsIjoiaHR0cHM6Ly9hc3NldHMuYnJhaW50cmVlZ2F0ZXdheS5jb20iLCJhc3NldHNVcmwiOiJodHRwczovL2NoZWNrb3V0LnBheXBhbC5jb20iLCJkaXJlY3RCYXNlVXJsIjpudWxsLCJhbGxvd0h0dHAiOnRydWUsImVudmlyb25tZW50Tm9OZXR3b3JrIjpmYWxzZSwiZW52aXJvbm1lbnQiOiJvZmZsaW5lIiwidW52ZXR0ZWRNZXJjaGFudCI6ZmFsc2UsImJyYWludHJlZUNsaWVudElkIjoibWFzdGVyY2xpZW50MyIsImJpbGxpbmdBZ3JlZW1lbnRzRW5hYmxlZCI6dHJ1ZSwibWVyY2hhbnRBY2NvdW50SWQiOiJ3aGlsZGNhdWdodCIsImN1cnJlbmN5SXNvQ29kZSI6IlVTRCJ9LCJtZXJjaGFudElkIjoicXBkbmJ3bmpicncyN2ttaiIsInZlbm1vIjoib2ZmIn0=',
        txn: {
          billing: {
            firstName: '',
            lastName: '',
            streetAddress: '',
            extendedAddress: '',
            city: '',
            state: '',
            postalCode: ''
          },
          customer: {
            firstName: '',
            lastName: '',
            phone: '',
            email: ''
          },
          options: {
            submitForSettlement: true
          },
          payment: {
            amount: '',
            merchantAccountId: 'whildcaught',
            paymentMethodNonce: '',
            paymentMethodToken: '',
            paypal: ''
          }
        }

      }
    },
    methods: {
      btHFError(message) {
        console.error(message);
        // do something with the error message
      },
      btHFPayload(payload) {
        console.log(payload);
        // do something with the token payload
      },
      submitTransaction() {
        this.$emit('tokenize');
      }
    }
  }
</script>

<style>
  * {
    font-family: monospace;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
  }

  body {
    margin: 60px;
  }
  h1, h2 {
    font-weight: normal;
  }

  button, input[type="submit"] {
    background-color: #FF6B00;
    display: inline-block;
    border: none;
    padding: 1rem 1.5rem;
    margin: 1px;
    color: white;
    font-size: 1.25rem;
    cursor: pointer;
  }

  button[type="submit"] {
    margin-top: 1rem;
    background-color: #44a948;
  }

  button[type="submit"]:disabled {
    background-color: #E6E6E6;
  }

  .line {
    width: 100%;
    display: flex;
    flex-direction: row;
  }

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
    padding: 1rem;
  }

  label {
    font-size: 16px;
    display: block;
    text-align: left;
    font-weight: bold;
    padding: 0.25rem;
    width: 100%;
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
