# Elm Service Worker Starter
This is a starting template for an Elm webpack configuration that supports offline assets via a service worker (SW), as well as a PWA manifest.
It is inspired by `elm-webpack-starter`, with additions to handling assets.

**This template is not about code splitting or server-side rendering (SSR), or prescribing how to handle the offline state in your app logic. PWA is a pretty loaded term...**

The repo itself is also meant as my notes on PWAs, even if you don't intend to use the template itself.

## Why?
I found while creating `ephemeral` that I had put together a little webpack monster (from elm-webpack-starter), which gave me some powerful features, with relatively thin abstractions.

I have previously implemented Service Workers from basic blocks, and wondered how to expand as assets were added to my app.
Adding the plugins seemed well-aligned with the "manual" steps I was previously taking; this repo is a further template on using those plugins.

### Naming things...
I had a tough time naming this repo.
- `elm-serviceworker-starter` might imply that it is a native handling of SW in elm
- `elm-offline-starter` makes similar promises about handling the offline state in application logic, which it does not prescribe
- `elm-pwa-starter` similarly might be a misnomer, since it might imply the more advanced loading of code splitting and SSR.

Still, in the context of "starter" and "PWA minimum", the latter is the one I went with.
If you have an alternative name, please open an issue and let's discuss :)

## How?
This relies heavily on Webpack and some plugins for it (`offline-plugin`, `pwa-manifest-plugin`).

If you are averse to webpack (I am too, sometimes), then this project will not help you much.
If you are still interested in the main operations, read on, and perhaps circle back after you have implemented some of the functionality and see common patterns.
Even if you do like webpack and are happy not knowing details, reading on is still advised.

### Service Worker Assets: Offline Plugin
Settting up Service Workers is rather straightforward from first principles!
Essentially, they are a way of specifying *what* to cache and *when*, in order to make your application available offline.
I suggest you look at `sw-toolbox.js`, and then at the different caching strategies, to get a better understanding of this.
Quickly, it boils down to *knowing which assets you have*, and *what their priorities are*.
If you pair that with *fingerprinting* and some *asset discovery* (since Webpack knows your assets by its nature), you can see that `offline-plugin` is an abstracted way of specifying these strategies and automating addition of assets.

Still not convinced?
Run the project and inspect the generated sw.js; it is quite simple :)

### PWA Manifest
The PWA Manifest is a specification for letting browsers know details about your app, such as the theme colour, icons and so on.
It is quite simple to write (there are maybe 20 lines in it), so why the webpack plugin?
Simply put, I like to keep things in one place!

Run the project and inspect the generated app.manifest; you could easily write it yourself.

## What to customise
Following from the above, here is what you can customise, and where:
- Manifest details
- SW strategy, if you want, in ...
- Write your Elm app

## Running
### Development

### Production Build

## Will this help me make a Progressive Web App (PWA)?
Part of a PWA is registering with a SW, keeping the assets while offline, as well as using a manifest for "installing" the application.
In that sense, yes, this helps you start a PWA.
There are, however, other things to consider in creating a PWA:
  - Actually have an SPA, with client-side routing etc.
  - How to handle an offline data state
  - Incremental loading techniques

### Offline data?
This is up to you in your code logic.
It can be as simple as "sorry, you are offline, tough luck", "here is an offline view", to "here are some operations we will perform once you are back".
I am not aware of any huge solutions simply in Elm at the moment, but the design space is there!

As an example, `ephemeral` uses the PouchDB/CouchDB combination.
That means a lot of JS interop, but allows us to leverage established libraries, while still keeping Elm's superior state handling via types.

### SPA?
This repo does not currently prescribe a template for how to get an SPA, as that is due to change soon (tm).
You can look at Richard Feldman's [elm-spa-example](https://github.com/rtfeldman/elm-spa-example) for a demonstration.

The gist of it is that you split the separate pages, link them in the Main and Routing modules, and liberally apply Html.map, Cmd.map, Sub.map to delegate to the appropriate module.
There is also a Routing module that handles URLs, but quite some duplication that you have to look out for.
Richard Feldman's presentation is also a good motivator for how that template works.

A WIP skeleton using this technique is soon to be found at: [elm-spa-skeleton](https://github.com/fpapado/elm-spa-skeleton).

### More advanced loading techniques
Another part of PWAs is incrementally loading assets as they are needed, instead of a single dump that might take a while to load on slower connections.
Code splitting is the general term for this pattern, allowing you to load just the data needed for the current route, and perhaps preload assets for other pages, once those are done loading.
**This is not currently supported in Elm**.
If I had to guess, it would be because of the runtime present and the relationships between modules.
The upside is that this is probably solvable quite elegantly with the compiler, and apparently it is in the pipeline for the next version.

You can still probably codesplit your JS payloads if that is a significant component.
TODO: Refer to the webpack docs for how to do that.

TODO: Notes for SSR for initial payload
Running the code with some model, generating it as a string and passing it to the browser.
Experiments exist for this.
