<template>
    <span>
        <button v-if="this.$recaptcha.config.enabled"
            :class="'g-recaptcha '+this.className"
            :data-sitekey=this.$recaptcha.config.siteKey
            :data-callback=this.callback>
            <slot>Submit</slot>
        </button>
        <button v-else :class=this.className>
            <slot>Submit</slot>
        </button>
    </span>
</template>
<script>
import Vue from 'Vue';

const defaultOptions = {
    enabled: true,
    siteKey: '',
    url: '/recaptcha.config.json',
}

export default {
    install(Vue, opts)
    {
        const options = { ...defaultOptions, ...opts };

        const root = new Vue({
            data: {
                config: options,
                render: createElement => createElement(this)
            }
        });

        Vue.prototype.$recaptcha = root;

        Vue.component('recaptcha', this);
        
        if((options && !options.siteKey) || !options)
        {
            axios.get(options.url)
                .then(options => {
                    if(!options.data.siteKey)
                        throw `siteKey not found in Recaptcha config file - ${options.url}`;
                    Vue.prototype.$recaptcha.config = options.data;
                })
                .catch(error => {
                    if(error == `siteKey not found in Recaptcha config file - ${options.url}`)
                        throw error;
                    else
                        throw `An error occured while trying to load Reacptcha config file - ${options.url}. Please find more details at https://www.npmjs.com/package/@mathewparet/vue-recaptcha-invisible#configuration. ${error}`;
                });
        }
    },
    props: {
        className: {
            required: false,
            type: String,
        }
    },
    computed: {
        callback() {
            return "recaptchaSubmit"+this.formId;
        },
    },
    data()
    {
        return {
            formId: null,
        }
    },
    methods: {
        loadRecaptcha() {
            let recaptchaScript = document.createElement('script');
            recaptchaScript.setAttribute('src', 'https://www.google.com/recaptcha/api.js');
            document.head.appendChild(recaptchaScript);
        },
        defineCallback() {
            self = this
            window['recaptchaSubmit'+this.formId] = function() {
                document.getElementById(self.formId).submit();
            }
        },
    },
    mounted() {
        this.formId = $(this.$el).closest('form')[0].id;
        this.loadRecaptcha(); // load the recaptcha script
        this.defineCallback(); // define the window callback function
    }
}
</script>
