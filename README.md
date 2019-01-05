# @mathewparet/vue-recaptcha-invisible

> A Vue.JS component for invisible recaptcha that falls back to native button if recaptcha is disabled in config, making it easy to have different settings for different environments.

## Installation

``` bash
# instal the module
npm install @mathewparet/vue-recaptcha-invisible
```

## Registration

```js
import Recaptcha from '@mathewparet/vue-recaptcha-invisible';
Vue.use(Recaptcha, { 
    enabled: true, 
    siteKey: '<site key from Google>'
});
```

### Configuration - ```recaptcha.config.json```

If you do not wish to pass the configuration while initializing the component - i.e, if you wish to register the component as ```Vue.use(Recaptcha)``` then you can define a JSON file of the below format and save it directly under your ```public``` directory. It must have the exact filename ```recaptcha.config.json```.

```json
{
    "siteKey": "<sitekey received from Google>",
    "enabled: true
}
```

For your convinience, a sample ```recaptcha.config.json``` file is placed in your project root (_not the ```public``` directory_) directory when you run ```npm install @mathewparet/vue-recaptcha-invisible```. You can copy this file to the ```public``` directory and modify the configuration as needed.

**Note** - *You need to add your```<public_dir>/<recaptcha-config-file>.json```* to ```.gitignore``` since this configuration should be different for each environment.

If you wish to use a custom file name for the config file, then you will need to pass the same as an option when registering the compoenent, like:

```js
import Recaptcha from '@mathewparet/vue-recaptcha-invisible';
Vue.use(Recaptcha, {
    url: '/my-custom-recaptcha-conf-file.json'
})
```

## Usage
```html
<recaptcha class-name="btn btn-primary">Submit</recaptcha> 
```

### Note
Your ```<form>``` element __MUST__ have an ```ID``` defined

### Attributes
Name | Type | Required | Description
--- | --- | --- | ---
```class-name``` | String | No | CSS Class name to be applied.
