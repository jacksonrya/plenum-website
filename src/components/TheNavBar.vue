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

<script lang="ts">
import {Component, Emit, Vue} from 'vue-property-decorator';
import TheMainMenu from '@/components/TheMainMenu';
import { mapGetters } from 'vuex';
import TheLogo from './TheLogo';

@Component({
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
    }
})

// The main navigation bar for the app
export default class TheNavBar extends Vue {
    private navBarWidth: number = 240; // Width of the navigation bar

    constructor() {
        super();
    }

    @Emit('openContent')
    private openContent(routerLinkLocation: string, keyboardEvent?: boolean = true) {}

    @Emit('revertMenuSession')
    private revertMenuSession(): void {}
}
</script>

<style lang="scss" scoped>
    @import '../styles/_settings';

    .navbar__main-menu {
        // @media screen and (max-width: $breakSmall) {
        //     display: none;
        // }
        margin-top: $headingCardHeight;

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

