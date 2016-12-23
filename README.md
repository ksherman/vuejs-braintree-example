# VueJS Braintree Integration Example

Welcome!

I'm working on example integrations powered by VueJS + Webpack.

Currently, I have:

* a basic Drop-in UI Integration that uses v2 of the JS SDK
* Hosted Fields integration that uses v3 of the JS SDK

**NOTE:** This does _not_ include a server-side compnent, a requirement for a) obtaining a Client Token and b) cannot make API requests. You will need to supply your own server-side integration.

**Note:** Because the npm repo library for Braintree's JavaScript SDK has the same name for v2 and v3, but v3 doesn't currently include a Drop-in, I have included a local copy of JS v2.30 to require into the library. This isn't ideal, but functional for now.

### To Do:
* Re-write more of the braintree.setup() code to be more Vue-comonent-like.
* I'd like the Hosted Fields v3 .vue component to be distributable and more flexible.
* Planning to incorporate a read-made AJAX call for the form submission.
* I'm up for suggestions! Create an issue on the repo.
