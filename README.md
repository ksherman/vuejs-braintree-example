# VueJS Braintree Integration Example

Welcome!

I'm working on example integrations powered by VueJS + Webpack.

Currently, I have:

* a basic Drop-in UI Integration that uses [v2 of the JS SDK](https://developers.braintreepayments.com/start/hello-client/javascript/v2)
* Hosted Fields integration that uses [v3 of the JS SDK](https://developers.braintreepayments.com/start/hello-client/javascript/v3)

**NOTE:** This does _not_ include the [server-side component](https://developers.braintreepayments.com/start/hello-server/), which is need to a) obtaining a Client Token and b) make API requests. You will need to supply your own server-side integration. I recommend [Node](https://developers.braintreepayments.com/start/hello-server/node) or [PHP](https://developers.braintreepayments.com/start/hello-server/php), depending what you like!

**Note:** Because the npm repo library for Braintree's JavaScript SDK has the same name for v2 and v3, but v3 doesn't currently include a Drop-in, I have included a local copy of JS v2.30 to require into the library. This isn't ideal, but functional for now.

![Example](http://i.giphy.com/26FLa32g5mYDgy8Ew.gif)

### To Do:
- [x] Re-write more of the braintree.setup() code to be more Vue-comonent-like.
- [x] I'd like the Hosted Fields v3 .vue component to be distributable and more flexible.
- [] Planning to incorporate a read-made AJAX call for the form submission.
- [] I'm up for suggestions! Create an issue on the repo.

Disclaimer: I work at Braintree as an Overnight Technical Support rep, but this is _not_ an official library or example integration. https://developers.braintreepayments.com/