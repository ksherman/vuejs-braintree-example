<template>
  <div id="app">
    <h1>VueJS Braintree Integration</h1>
    <button @click="integration = 'dropin'">Drop-In JSv2</button>
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
      clientToken: 'eyJ2ZXJzaW9uIjoyLCJhdXRob3JpemF0aW9uRmluZ2VycHJpbnQiOiJkMzQ4N2NmYmVkYzkzMjhiYzkxMzNiYjQ2MDkxMTdmOWFiODFjNTQzYjQ0OTMwMWEzN2UxZjRmOTM3ODUxOTMzfGNyZWF0ZWRfYXQ9MjAxNi0xMi0yM1QwOToyNDoxNC4yMjI2MjQ1NjErMDAwMFx1MDAyNm1lcmNoYW50X2lkPXFwZG5id25qYnJ3MjdrbWpcdTAwMjZwdWJsaWNfa2V5PTM3cmY1ZmR5M2h2a3Z6NDYiLCJjb25maWdVcmwiOiJodHRwczovL2FwaS5zYW5kYm94LmJyYWludHJlZWdhdGV3YXkuY29tOjQ0My9tZXJjaGFudHMvcXBkbmJ3bmpicncyN2ttai9jbGllbnRfYXBpL3YxL2NvbmZpZ3VyYXRpb24iLCJjaGFsbGVuZ2VzIjpbXSwiZW52aXJvbm1lbnQiOiJzYW5kYm94IiwiY2xpZW50QXBpVXJsIjoiaHR0cHM6Ly9hcGkuc2FuZGJveC5icmFpbnRyZWVnYXRld2F5LmNvbTo0NDMvbWVyY2hhbnRzL3FwZG5id25qYnJ3MjdrbWovY2xpZW50X2FwaSIsImFzc2V0c1VybCI6Imh0dHBzOi8vYXNzZXRzLmJyYWludHJlZWdhdGV3YXkuY29tIiwiYXV0aFVybCI6Imh0dHBzOi8vYXV0aC52ZW5tby5zYW5kYm94LmJyYWludHJlZWdhdGV3YXkuY29tIiwiYW5hbHl0aWNzIjp7InVybCI6Imh0dHBzOi8vY2xpZW50LWFuYWx5dGljcy5zYW5kYm94LmJyYWludHJlZWdhdGV3YXkuY29tL3FwZG5id25qYnJ3MjdrbWoifSwidGhyZWVEU2VjdXJlRW5hYmxlZCI6ZmFsc2UsInBheXBhbEVuYWJsZWQiOnRydWUsInBheXBhbCI6eyJkaXNwbGF5TmFtZSI6IldoaWxkIENhdWdodCIsImNsaWVudElkIjpudWxsLCJwcml2YWN5VXJsIjoiaHR0cDovL2V4YW1wbGUuY29tL3BwIiwidXNlckFncmVlbWVudFVybCI6Imh0dHA6Ly9leGFtcGxlLmNvbS90b3MiLCJiYXNlVXJsIjoiaHR0cHM6Ly9hc3NldHMuYnJhaW50cmVlZ2F0ZXdheS5jb20iLCJhc3NldHNVcmwiOiJodHRwczovL2NoZWNrb3V0LnBheXBhbC5jb20iLCJkaXJlY3RCYXNlVXJsIjpudWxsLCJhbGxvd0h0dHAiOnRydWUsImVudmlyb25tZW50Tm9OZXR3b3JrIjp0cnVlLCJlbnZpcm9ubWVudCI6Im9mZmxpbmUiLCJ1bnZldHRlZE1lcmNoYW50IjpmYWxzZSwiYnJhaW50cmVlQ2xpZW50SWQiOiJtYXN0ZXJjbGllbnQzIiwiYmlsbGluZ0FncmVlbWVudHNFbmFibGVkIjp0cnVlLCJtZXJjaGFudEFjY291bnRJZCI6IndoaWxkY2F1Z2h0IiwiY3VycmVuY3lJc29Db2RlIjoiVVNEIn0sImNvaW5iYXNlRW5hYmxlZCI6ZmFsc2UsIm1lcmNoYW50SWQiOiJxcGRuYnduamJydzI3a21qIiwidmVubW8iOiJvZmYifQ=='
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
  border: none;
  padding: 1rem 1.5rem;
  margin: 1px;
  color: white;
  font-size: 1.25rem;
  cursor: pointer;
}

input[type="submit"] {
  margin-top: 1rem;
  background-color: #44a948;
}

input[type="submit"]:disabled {
  background-color: #E6E6E6;
}
</style>
