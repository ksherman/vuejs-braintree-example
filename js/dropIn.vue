<template>
  <div class="constrain">
    <form id="checkout" method="post" action="/checkout">
      <h2>Drop-in UI</h2>
      <div id="payment-form"></div>

      <label>How many monies?</label>
      <input type="text" name="checkoutValue" class="hosted-field" placeholder="$10">

      <button class="" type="submit" value="SEND THE MONIES" style="padding-top: 1rem;">SEND THE MONIES</button>
    </form>
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
      clientToken: this.token
    }
  },
  methods: {
    btDropin() {
      var braintree2 = require('../js/bt-web-v2/braintree-2.30.0.min.js');
      console.log('Braintree Drop-in UI using v2');
      braintree2.setup(this.clientToken, 'dropin', {
        container: 'payment-form'
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