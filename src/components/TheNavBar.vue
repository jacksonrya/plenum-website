<template>
    <div>
        <nav
            class="navbar__main-menu"
            :class="{'navbar__main-menu--hidden': $mq == 'sm' && !mobileMenuOpen}"
            role="navigation"
            aria-label="Plenum Main Navigation"
        >
            <the-main-menu
                :menuItems="menuTree"
                @revertMenuSession="revertMenuSession"
                @openContent="openContent"
            ></the-main-menu>
        </nav>
    </div>
</template>

<script>
import TheMainMenu from '@/components/TheMainMenu';
import { mapGetters } from 'vuex';
import TheLogo from './TheLogo';

export default {
    components: {
        TheLogo,
        TheMainMenu
    },
    computed: {
        ...mapGetters({
            "menuTree": 'menuTree/menuTree'
        })
    },
    props: {
        mobileMenuOpen: Boolean
    },
    data: function() {
        return {
            navBarWidth: 240
        }
    },
    methods: {
        /**
         * @param {string} routerLinkLocation
         * @param {boolean} keyboardEvent
         */
        openContent(routerLinkLocation, keyboardEvent = true) {
            this.$emit('openContent', routerLinkLocation, keyboardEvent)
        },
        /**
         * 
         */
        revertMenuSession: function() {
            this.$emit('revertMenuSession')
        }
    }
}
</script>

<style lang="scss" scoped>
    @import '../styles/_settings';

    .navbar__main-menu {
        // @media screen and (max-width: $breakSmall) {
        //     display: none;
        // }
        margin-top: calc(#{$headingCardHeight} - 100px); // 100px = top-margin on parent

        @media screen and (max-width: $breakSmall) {
            position: relative;
            width: 100%;
            height: 100%;
            margin-top: 0;
            padding-top: 25px;
            background: $bgColor;
        }
    }

    .navbar__main-menu--hidden {
        // transition: 300ms linear;
        // opacity: 0;
    }

    .navbar__about {
        position: absolute;
        bottom: 0;
        left: 0;
        width: calc(#{$navBarWidth} - 30px);

        padding: 15px 15px 0 15px;

        font-family: $sansSerifFont;
        font-weight: bold;
    }

    .navbar__about p {
        margin: 1em 0;

        vertical-align: middle;
        font-size: 15.41px; // TODO: make responsive
        text-align: left;
        line-height: 20px;
        text-indent: unset;
    }

</style>

