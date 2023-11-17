<template>
    <div class="pa-3">
        <home-page  v-if="inHomePage"/>
        <about-page v-else-if="inAboutPage"/>
        <contact-us-page v-else-if="inContactUsPage"/>
        <Post v-else-if="posts"/>
    </div>
</template>


<script>
    import { defineComponent } from 'vue';
    import HomePage      from "../pages/HomePage.vue";
    import AboutPage     from "../pages/AboutPage.vue";
    import ContactUsPage from "../pages/ContactUsPage.vue";
    import Posts from "../pages/Posts.vue";
    import posts from "../pages/Posts.vue";


    export default defineComponent({
        name: "Main",

        /** DATA */
        data() {
            return {

            }
        },

        /** PROPS */
        props: {
            page: {
                type    : String,
                required: true
            }
        },

        /** COMPONENTS */
        components: {
            'home-page'      : HomePage,
            'about-page'     : AboutPage,
            'contact-us-page': ContactUsPage,
            'Post':Posts,
        },

        /** COMPUTED PROPERTIES */
        computed: {
          posts() {
            return posts
          },
            /**
             * Computed page is 'home' or not.
             * @returns {boolean}
             */
            inHomePage() {
                return this.page === 'home';
            },

            /**
             * Computed page is 'about' or not.
             * @returns {boolean}
             */
            inAboutPage() {
                return this.page === 'about';
            },

            /**
             * Computed page is 'contact' or not.
             * @returns {boolean}
             */
            inContactUsPage() {
                return this.page === 'contact';
            }
        }
    })
</script>


<style scoped>

</style>
