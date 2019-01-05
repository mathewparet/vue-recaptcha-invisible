<template>
    <span>
        <button v-if="this.$recaptcha.enabled"
            :class="'g-recaptcha '+this.className"
            :data-sitekey=this.$recaptcha.siteKey
            :data-callback=this.callback>
            <slot>Submit</slot>
        </button>
        <button v-else :class=this.className>
            <slot>Submit</slot>
        </button>
    </span>
</template>
<script>
export default {
    install(Vue, options)
    {
        Vue.component('recaptcha', this);
        
        Vue.prototype.$recaptcha = {};

        Vue.prototype.$recaptcha.enabled = options && options.enabled ? options.enabled : true;
        Vue.prototype.$recaptcha.siteKey = options && options.siteKey ? options.siteKey : null;

        if((options && !options.siteKey) || !options)
        { 
            let url = options && options.url ? options.url : '/recaptcha.config.json'
            axios.get(url)
                .then(options => {
                    Vue.prototype.$recaptcha = options.data;
                    if(!options.data.siteKey)
                        throw `siteKey not found in Recaptcha config file - ${url}`;
                })
                .catch(error => {
                    if(error == `siteKey not found in Recaptcha config file - ${url}`)
                        throw error;
                    else
                        throw `An error occured while trying to load Reacptcha config file - ${url}. Please find more details at https://www.npmjs.com/package/@mathewparet/vue-recaptcha-invisible#configuration. ${error}`;
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
        if(this.$recaptcha.enabled)
        {
            this.loadRecaptcha(); // load the recaptcha script
            
            this.defineCallback(); // define the window callback function
        }
    }
}
</script>
