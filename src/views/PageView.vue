<template>
    <main ref="home" class="content-container">
        <!-- TODO: use component associated with given content-type -->
        <basic-page
            v-if="page && Object.keys(page).length > 0"
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
        page: {}
    },
    /**
     * When view is mounted, retrieve article.
     */
    mounted: function() {
        this.getPage()

        vm.$watch('$route.path', function(newVal, oldVal) {
            this.page = undefined;
            this.getPage();
        })
    },
    methods: {
        /**
         * 
         */
        getPage() {
            let menuPage = this.getPageMetaDataFromMenuTree(this.$route.path);

            let pages = this.$store.getters['pages/getPages'];

            if (menuPage.uuid && !pages.some(page => page.uuid === menuPage.uuid)) {
                this.$store.dispatch('pages/fetchPage', menuPage.uuid)
                    .then(res => {
                        pages = this.$store.getters['pages/getPages'];
                        this.page = pages.find(page => page.uuid === menuPage.uuid);
                    });
            } else {
                this.page = pages.find(page => page.uuid === menuPage.uuid);
            }
        },
        /**
         * Returns simplified data via the menu tree of the page represented in the provided path.
         * @param {string} path Url path to a page e.g. '/section/page'.
         * @return
         */
        getPageMetaDataFromMenuTree(path) {
            const menuTree = this.$store.getters['menuTree/menuTree'];

            let pathParts;
            let section;
            let pageTitle;
            let menuPage;

            let slashCount = (path.match(/\//g) || []).length;
            if (slashCount === 2) {
                pathParts = path.slice(1).split('/');
                section = pathParts[0].replace(/-/g, ' ').replace(/\b\w/g, l => l.toUpperCase());
                pageTitle = pathParts[1].replace(/-/g, ' ').replace(/\b\w/g, l => l.toUpperCase());

                menuPage = menuTree
                    .find(mainMenuItem => mainMenuItem.title.toLowerCase() === section.toLowerCase())
                    .submenu.find(page => page.title.toLowerCase() === pageTitle.toLowerCase());
            } else if (slashCount === 1) {
                pageTitle = path.slice(1).replace(/-/g, ' ').replace(/\b\w/g, l => l.toUpperCase());

                menuPage = menuTree
                    .find(mainMenuItem => mainMenuItem.title.toLowerCase() === pageTitle.toLowerCase())
            }

            return menuPage;
        }
    }
}
</script>

<style lang="scss" scoped>

</style>
