<template>
  <div :class="wrapperClass">

    <div v-show="!hostedFieldsInstance" class="loader" id="loader-1"></div>

    <div v-show="hostedFieldsInstance">
      
      <div class="line" v-if="collectCardHolderName">
        <label for="cardholder">Card Holder
          <input type="text" class="input-field" id="cardholder" name="cardholder" placeholder="Name">
        </label>
      </div>

      <div class="line">
        <label for="card-number">Card Number
          <div class="input-field" id="number"></div>
        </label>

        <label for="postal" v-if="collectPostalCode">Postal Code
          <input type="text" class="input-field" id="postal" name="postal" placeholder="11111">
        </label>
      </div>

      <div class="line">
        <label for="cvv">CVV
          <div class="input-field" id="cvv"></div>
        </label>

        <label for="expiration-date">Expiration Date
          <div class="input-field" id="expiration-date"></div>
        </label>
      </div>

    </div>

  </div>
</template>

<script>
  export default {
    props: {
      authToken: {
        value: String,
      },
      wrapperClass: {
        value: String,
      },
      loaderClass: {
        value: String,
      },
      inputClass: {
        value: String,
      },
      collectCardHolderName: {
        value: Boolean,
      },
      collectPostalCode: {
        value: Boolean,
      },
      enableDataCollector: {
        value: Boolean,
      }
    },
    created() {
      this.createBT();

      this.$parent.$on('tokenize', () => {
        this.tokenizeHF();
      });
    },
    data () {
      return {
        errorMessage: '',
        clientInstance: '',
        vaultedPaymentMethods: [],
        hostedFieldsInstance: '',
        tokenizePayload: '',
        dataCollectorPayload: '',
      }
    },
    methods: {
      createBT () {
        const client = require('braintree-web/client');
        client.create({
          authorization: this.authToken
        }, (clientErr, clientInstance) => {
          if (clientErr) {
            this.errorMessage = 'There was an error setting up the client instance. Message: ' + clientErr.message;
            this.$emit('bthferror', this.errorMessage);
            return;
          } else {
            this.clientInstance = clientInstance
            this.createHF();
            this.createVaultManager();

            if (this.enableDataCollector) {
              this.dataCollectorCreate();
            }
          }
        });
      },
      createVaultManager() {
        const vaultManager = require('braintree-web/vault-manager');
        vaultManager.create({
          client: this.clientInstance,
        }, (err, vaultInstance) => {
          if (err) {
            this.errorMessage = 'There was an error setting up the client instance. Message: ' + err.message;
            this.$emit('bthferror', this.errorMessage);
            return;
          }

          console.log(vaultInstance);

          vaultInstance.fetchPaymentMethods((err, paymentMethods) => {
            console.log(paymentMethods);
            this.vaultedPaymentMethods = paymentMethods;
          });
        });
      },
      createHF () {
        const hostedFields = require('braintree-web/hosted-fields');
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
              placeholder: '4111 1111 1111 1111',
            },
            cvv: {
              selector: '#cvv',
              placeholder: '123',
            },
            expirationDate: {
              selector: '#expiration-date',
              placeholder: '10/2019',
            },
          },
        }, (hostedFieldsErr, hostedFieldsInstance) => {
          if (hostedFieldsErr) {
            // Handle error in Hosted Fields creation
            this.errorMessage = 'There was an error setting up the hosted fields! Message: ' + hostedFieldsErr.message;
            this.$emit('bthferror', this.errorMessage);
            return;
          } else {
            this.$emit('bthfready');
            this.hostedFieldsInstance = hostedFieldsInstance;
          }

        });
      },
      tokenizeHF () {
        const additionalFields = {
          cardholderName: '',
          billingAddress: {
            postalCode: '',
          },
        };
        if (this.collectCardHolderName) {
          additionalFields.cardholderName = document.querySelector('#cardholder').value;
        }
        if (this.collectPostalCode) {
          additionalFields.billingAddress.postalCode = document.querySelector('#postal').value;
        }
        console.log(additionalFields);
        this.hostedFieldsInstance.tokenize(additionalFields, (tokenizeErr, payload) => {
          if (tokenizeErr) {
            this.errorMessage = 'There was an error tokenizing! Message: ' + tokenizeErr.message;
            this.$emit('bthferror', this.errorMessage);
            return;
          }

          this.tokenizePayload = payload;
          this.$emit('bthfpayload', payload);
          this.teardownHF();

        });
      },
      teardownHF () {
        this.hostedFieldsInstance.teardown( (teardownErr) => {
            if (teardownErr) {
              this.errorMessage = 'There was an error tearing it down! Message: ' + teardownErr.message;
              this.$emit('bthferror', this.errorMessage);
              return;
            } else {
              this.hostedFieldsInstance = '';
              return;
            }
        });
      },
      dataCollectorCreate() {
        const dataCollector = require('braintree-web/data-collector');
        dataCollector.create({
          client: this.clientInstance,
          kount: true,
        }, (dataCollectorErr, dataCollectorInstance) => {
          if (dataCollectorErr) {
            this.errorMessage = 'There was an error setting up the data collector! Message: ' + dataCollectorErr.message;
            this.$emit('bthferror', this.dataCollectorErr);
            return;
          }

          this.$emit('device.data.payload', dataCollectorInstance.deviceData);
          this.dataCollectorPayload = dataCollectorInstance;

        });

      }
    },
  };
</script>

<style>
  .loader{
    width: 50px;
    height: 50px;
    border-radius: 100%;
    position: relative;
    margin: 0 auto;
  }

  /* LOADER 1 */

  #loader-1:before, #loader-1:after{
    content: "";
    position: absolute;
    top: -10px;
    left: -10px;
    width: 100%;
    height: 100%;
    border-radius: 100%;
    border: 10px solid transparent;
    border-top-color: #3498db;
  }

  #loader-1:before{
    z-index: 100;
    animation: spin 1s infinite;
  }

  #loader-1:after{
    border: 10px solid #ccc;
  }

  @keyframes spin{
    0%{
      -webkit-transform: rotate(0deg);
      -ms-transform: rotate(0deg);
      -o-transform: rotate(0deg);
      transform: rotate(0deg);
    }

    100%{
      -webkit-transform: rotate(360deg);
      -ms-transform: rotate(360deg);
      -o-transform: rotate(360deg);
      transform: rotate(360deg);
    }
  }
</style>