# Aurelia and ADAL (and other "non-module" libraries)

First up, if you're not already using the CLI for Aurelia, 
then you should make the transition: [https://github.com/aurelia/cli](https://github.com/aurelia/cli)

It is so much better than the old way (jspm/gulp/etc)!

## What's ADAL?
The Active Directory Authentication Library (ADAL) is a JavaScript Library
for authenticating with Active Directory in Azure.  This can be an AD that 
synchronised from on-premise, or even the new B2C offering.

To find out more about it, go here: [https://github.com/AzureAD/azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js)

I'm not going to talk about the benefits of using Azure AD or go into any
detail about how the authentication works under the covers.  The purpose of
this article is to discuss how to go about incorporating the library into Aurelia.
As the title suggests, the same steps can be used for other libraries that do not
use modules.

## How do I add it?
I have a small criticism of the team that created this is. They seem to be purely focused
on Angular, rather than making a generic library that can be used by any SPA framework.
This is reflected in their choice of NPM package name: [adal-angular](https://www.npmjs.com/package/adal-angular)

I think this was a bit short-sighted given the many other options out there (Aurelia, React, etc).  But, it
works, so I'll say no more about it!

So, back to the point.  To install into your Aurelia application, run the following command:
```batch
npm install --save adal-angular
```
This will add it to your list of dependencies.

Next you need to include this in your bundle.  Here's where things are not immediately obvious with the new CLI.
My first attempt was to add ```adal-angular``` into the list of dependencies in ```aurelia_project/aurelia.json```.
But, the errors this produced lead me to look at the source for ```adal.js``` where I realised that modules are 
not used.  Instead, they stick ```AuthenticationContext``` into the global namespace.

I was stumped... briefly. Until I remembered how the bundles were built by the CLI. When the Aurelia team first
released the CLI I set aside a day to dig into it and try to understand it's workings.  While I don't consider myself
an expert, the time I spent on this helped my understanding enourmously.  One of the major differences from before
was that they dropped ```jspm``` and started using RequireJs. I found this in the ```build/bundles/prepend``` part of 
```aurelia_project/aurelia.json```.  Any scripts that you place in the ```prepend``` array will be inserted at the beginning
of the bundle that is created.  This is where I put ```adal-angular```:
```json
"bundles": [
    "prepend": [
          "node_modules/bluebird/js/browser/bluebird.core.js",
          "wwwroot/scripts/require.js",
          "node_modules/adal-angular/lib/adal.js"        
    ]
]
```