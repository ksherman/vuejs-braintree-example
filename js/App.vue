<template>
  <div id="app">
    <h1>VueJS Braintree Integration</h1>
    <button @click="integration = 'dropin'">Drop-In [Beta]</button>
    <button @click="integration = 'hostedfields'">Hosted Fields JSv3</button>
    
    <drop-in v-if="integration == 'dropin'" :token="clientToken"></drop-in>

    <hosted-fields v-if="integration == 'hostedfields'" :token="clientToken"></hosted-fields>

  </div>
</template>

<script>
import dropIn from './dropIn.vue';
import hostedFields from './hostedFields.vue';
export default {
  name: 'app',
  components: {
    dropIn,
    hostedFields
  },
  data () {
    return {
      integration: '',
      clientToken: 'sandbox_c8fdxdnb_qpdnbwnjbrw27kmj',
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
</style>
