<template>
    <main ref="home" class="content-container">
        <!-- TODO: use component associated with given content-type -->
        <basic-page
            v-for="(page, index) in pages"
            :key="`basic-page-${index}`"
            :page="page"
        ></basic-page>
    </main>
</template>

<script>
import BasicPage from '../components/content-types/BasicPage';

export default {
    components: {
        BasicPage
    },
    data: {
        pages: []
    },
    /**
     * When view is mounted, retrieve article
     */
    created: function() {
        this.initAboutPage()
    },
    methods: {
        initAboutPage: function() {
            if (this.$store.getters['pages/getAboutPage'].length === 0) {
                this.$store.dispatch('pages/initAboutPage')
                    .then(res => {
                        this.pages = this.$store.getters['pages/getAboutPage'];
                        this.$store.dispatch('setAppLoading', false);
                    });
            } else {
                this.pages = this.$store.getters['pages/getAboutPage'];
                this.$store.dispatch('setAppLoading', false);
            }
        }
    }
}
</script>

<style lang="scss" scoped></style>
