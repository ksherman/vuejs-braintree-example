# VueJS Braintree Integration Example

Welcome!

I'm working on example integrations powered by VueJS + Webpack.

Currently, I have:

* [NEW] a basic Drop-in UI Integration that uses [the Braintree Web Drop-in Beta](https://github.com/braintree/braintree-web-drop-in)
* Hosted Fields integration that uses [v3 of the JS SDK](https://developers.braintreepayments.com/start/hello-client/javascript/v3)

**NOTE:** This does _not_ include the [server-side component](https://developers.braintreepayments.com/start/hello-server/), which is need to a) obtaining a Client Token and b) make API requests. You will need to supply your own server-side integration. I recommend [Node](https://developers.braintreepayments.com/start/hello-server/node) or [PHP](https://developers.braintreepayments.com/start/hello-server/php), depending what you like!

![Example](http://i.giphy.com/26gsaQyL6pvcr857a.gif)

### To Do:
- [x] Re-write more of the braintree.setup() code to be more Vue-comonent-like.
- [x] I'd like the Hosted Fields v3 .vue component to be distributable and more flexible.
- [x] Add PayPal checkout
- [x] Added new Drop-in Web Beta!
- [ ] Add Data collector
- [ ] Planning to incorporate a read-made AJAX call for the form submission.
- [ ] I'm up for suggestions! Create an issue on the repo.

### Install:
* Clone the repo
* Go into the drop-in directory and `npm install` and `npm run build` to build the new Braintree Web Drop-in beta
* Back in the root of the cloned project, `npm install`
* After install, you have several options to run the example:
	`npm run dev`: opens a browser window with hot module reload
	`npm run build`: builds the `/dist` directory with the built scripts

Disclaimer: I work at Braintree as an Overnight Technical Support rep, but this is _not_ an official library or example integration. https://developers.braintreepayments.com/
