<template>
    <ul
        class="main-menu"
        :class="{'main-menu--expanded': $mq == 'sm' || navHovered || anyMenuIsOpen || focusedIndex > -1, 'main-menu--active': anyMenuIsOpen}"
        role="menubar"
        aria-label="Plenum Main Navigation"
        :aria-expanded="(navHovered || focusedIndex !== -1).toString()"

        @mouseover="handleNavHoverEvent()"
        @mouseleave="navHovered = false"
    >
        <li
            v-for="(menu, index) in menuItems"
            :id="menu.title.toLowerCase().replace(' ', '-') + '-main-menu-item'"
            :key="index"

            class="main-menu__menu-item"
            :class="{'main-menu__menu-item--disabled': menu.disabled, 'main-menu__menu-item--active-hovered': menu.hovered || menu.expanded}"
            :style="{background: menu.color}"

            @mouseenter="handleMenuItemHoverEvent($event, menu)"
            @mouseleave="handleMenuItemHoverEvent($event, menu)"
        >

            <!-- If a submenu exists, make a sub-menuitem; else, make a router-link -->
            <a
                v-if="menu.submenu && Object.getOwnPropertyNames(menu.submenu).length > 1"
                :id="'main-menu-item-' + index"
                :key="'link-to-' + menu.title.replace(' ', '-')"

                class="focusable"

                role="menuitem"
                aria-haspopup="true"
                :aria-expanded="menu.expanded.toString()"
                :tabindex="index === focusedIndex || (allFlyOutsAreClosed && index === 0) ? '0' : '-1'"

                v-focus="index === focusedIndex"

                @click="menu.expanded ? closeMainMenuFlyOut(menu, index, false) : openMainMenuFlyOut(menu, false)"
                @keydown.enter.prevent="menu.expanded ? closeMainMenuFlyOut(menu, index, true) : openMainMenuFlyOut(menu, true)"
                @keydown.space="menu.expanded ? closeMainMenuFlyOut(menu, index, true) : openMainMenuFlyOut(menu, true)"
                @keydown.esc="menu.expanded ? closeMainMenuFlyOut(menu, index, true) : null"
                @keydown.right="menu.expanded ? focusToFlyOut(menu) : openMainMenuFlyOut(menu, true)"
                @keydown.left="menu.expanded ? focusToFlyOut(menu, true) : openMainMenuFlyOut(menu, true, true)"
                @keydown.up.prevent="moveUp"
                @keydown.down.prevent="moveDown"
                @keydown.home.prevent="focusedIndex = 0"
                @keydown.end.prevent="focusedIndex = menuItems.length - 1"
                @keydown.tab="focusedIndex = -1"
                @keydown.alphabet="focusByLetter($event.key, index)"
                @focus="focusedIndex = index"
            >
                <span
                    class="main-menu__menu-item-content focusable__content"
                    tabindex="-1"
                >
                    {{ menu.title }}&nbsp;
                </span>
            </a>
            <router-link
                 v-else
                 :to="menu.path"
                 :id="'main-menu-item-' + index"
                 :key="'link-to-' + menu.title.toLowerCase().replace(' ', '-')"

                 class="focusable"

                 v-focus="index === focusedIndex"

                 role="link"
                 aria-haspopup="false"
                 :tabindex="index === 0 || index === focusedIndex ? '0' : '-1'"

                 @click.prevent.native="openContent('/' + menu.title.toLowerCase(), false)"
                 @keydown.right.prevent.native="openContent('/' + menu.title.toLowerCase())"
                 @keydown.enter.prevent.native="openContent('/' + menu.title.toLowerCase())"
                 @keydown.down.prevent.native="moveDown"
                 @keydown.up.prevent.native="moveUp"
                 @keydown.alphabet.native="focusByLetter($event.key, index)"
                 @keydown.tab.native="focusedIndex = -1"
                 @focus.native="focusedIndex = index"
            >
                <span
                    class="main-menu__menu-item-content focusable__content"
                    tabindex="-1"
                >
                    {{ menu.title }}&nbsp;
                </span>
            </router-link>

            <transition
                v-if="menu.submenu && menu.submenu.length > 0"
                name="fly-out-slide"
            >
                <main-menu-fly-out
                    v-show="menu.expanded"
                    class="fly-out"
                    :class="{ 'fly-out--open': menu.expanded, 'fly-out--hidden': menu.hidden }"
                    :menu="menu"

                    @activateMenu="openMainMenuFlyOut"
                    @toggleOpen="openMainMenuFlyOut"
                    @closeMainMenuFlyOut="closeMainMenuFlyOut"
                    @openArticle="openContent"
                    @collectionActivated="incrementPagesVisited"
                >
                </main-menu-fly-out>
            </transition>
        </li>
    </ul>
</template>

<script>
import MainMenuFlyOut from '@/components/MainMenuFlyOut';
import { mixin as focusMixin } from 'vue-focus'; // ignore 'cannot resolve' error
import { mapGetters } from 'vuex';

export default {
    mixins: [focusMixin],
    components: {
        MainMenuFlyOut,
    },
    computed: {
        ...mapGetters({
            "anyMenuIsOpen": 'menuTree/anyMenuIsOpen'
        }),
        /**
         * Returns whether or not all of the fly out submenus are closed.
         */
        allFlyOutsAreClosed: function() {
            return !this.menuItems.some((menuItem) => menuItem.expanded);
        }
    },
    props: {
        menuItems: {
            type: Array,
            default: []
        }
    },
    data: {
        navHovered: false,
        focusedIndex: -1
    },
    methods: {
        /**
         * The procedure to open an article.
         * @param {string} routerLinkLocation A url endpoint of the requested content.
         * @param {boolean} keyboardEvent If the open content event was caused by a keyboard, defaults true.
         */
        openContent: function(routerLinkLocation, keyboardEvent = true) {
            this.$emit('openContent')
        },
        /**
         * Calls the router to go back in history to the page that was loaded before a menu session.
         */
        revertMenuSession: function() {
            this.$emit('revertMenuSession')
        },
        /**
         * 
         */
        handleNavHoverEvent: function() {
            this.navHovered = true;
            this.focusedIndex = -1;
        },
        /**
         * @param {Event} event
         * @param {Object} menu
         */
        handleMenuItemHoverEvent: function(event, menu) {
            if (event.type === 'mouseenter') {
                menu.hovered = true;
            } else if (event.type === 'mouseleave') {
                menu.hovered = false;
            } else {
                console.error('incorrectly used function: handleMenuItemHoverEvent in TheMainMenu');
            }
        },
        /**
         * Sets the open menu and if the menu to open is already open, it closes.
         * @param {Object}  menuItem Main menu item to be opened or closed.
         * @param {boolean} isKeyboardEvent Whether or not the native DOM event was from a key press or not.
         * @param {boolean} toLastMenuItem Whether or not focus goes to the last menu item; defaults to first menu item.
         */
        openMainMenuFlyOut: function(menuItem, isKeyboardEvent, toLastMenuItem = false) {
            this.$store.dispatch('menuTree/openMainMenuItem', menuItem);
            if (isKeyboardEvent) {
                this.focusToFlyOut(menuItem, toLastMenuItem);
            }
        },
        /**
         * Closes the flyout submenu for the provided main menu item and optionally moves focus to the parent menu item.
         * @param {Object} menuItem Parent menu item of the to-be closed flyout submenu.
         * @param {number} menuItemIndex Index of the main menu item in the main menu list.
         * @param {boolean} returnFocusToMainMenuItem If the event that called this method was from a keyboard action.
         */
        closeMainMenuFlyOut: function(menuItem, menuItemIndex, returnFocusToMainMenuItem = false) {
            // Returns focus for keyboard navigation
            if (returnFocusToMainMenuItem) {
                setTimeout(() => {
                    this.focusedIndex = menuItemIndex;
                    // document.getElementById('main-menu-item-' + menuItemIndex).focus(); // FOCUS ON MAIN MENU ITEM
                    // TODO: change IDs from 'Publications-menu-item' to 'main-menu-item-1'
                }, 10);
            } else {
                this.focusedIndex = -1;
            }

            this.revertMenuSession();
        },
        // TODO: rename to event handler and does this belong here?
        /**
         * Increments the number of pages visited during a menu session.
         */
        incrementPagesVisited: function() {
            this.$store.dispatch('routerNav/pageVisited');
        },

        /*********************************/
        /* KEYBOARD NAVIGATION FUNCTIONS */
        /*********************************/
        // TODO: Find library to handle keyboard navigation, or globalize these functions for each menu

        /**
         * Move focus to the provided main menu item's flyout, default focuses on the first menu item of the flyout.
         * @param {Object}  menu Parent menu of the flyout to be focused on.
         * @param {boolean} toLastMenuItem Whether or not focus goes to the last menu item; defaults to first menu item.
         */
        focusToFlyOut: function(menu, toLastMenuItem = false) {
            const index = toLastMenuItem ? Object.keys(menu.submenu).length - 1 : 0;
            setTimeout(() => {
                document.getElementById(menu.title + '-fly-out-menu-item-' + index.toString()).focus();
            }, 10);
        },
        /**
         * Setter for the data of the index of the focused menu item.
         * @param {number} newVal the new value from which to set.
         */
        setFocusedIndex: function(newVal) {
            this.focusedIndex = newVal;
        },
        /**
         * Move focus down one menu item, or return to first menu item if at the end.
         */
        moveDown: function() {
            this.focusedIndex = this.focusedIndex === this.menuItems.length - 1 ? 0 : this.focusedIndex + 1;
        },
        /**
         * Move focus up one menu item, or return to last menu item if at the first.
         */
        moveUp: function() {
            this.focusedIndex = this.focusedIndex === 0 ? this.menuItems.length - 1 : this.focusedIndex - 1;
        },
        /**
         * Searches through the menu items and moves focus to the next menu item label that starts with the queried letter.
         * @param {string} queryLetter Single letter to be queried across menu item labels.
         * @param {number} currentlyFocusedIndex Index of the menu item that is currently focused.
         */
        focusByLetter: function(queryLetter, currentlyFocusedIndex) {
            // If not at the end of the menu... This logic follows WAI-ARIA keyboard nav standards
            if (currentlyFocusedIndex !== this.menuItems.length - 1) {
                let el = 'main-menu-item-' + this.menuItems
                    .slice(currentlyFocusedIndex + 1)
                    .findIndex((menuItem) => menuItem.title.toLowerCase().startsWith(queryLetter));
                document.getElementById(el).focus();
            }
        }
    }
}
</script>

<style lang="scss" scoped>
    @import "../styles/_settings";

    $menuItemHeight: 45px;
    $menuItemWidth: calc(210px + #{$borderWidth} * 2);

    .main-menu {
        width: $menuItemHeight;
        // Minus border width to accommodate left-outline while maintaining centered position when not open
        left: calc((50% - 45px / 2) - #{$borderWidth});

        position: relative;
        overflow: hidden;

        background: $bgColor;

        transition: width 0.3s ease;

        @media screen and (max-width: $breakSmall) {
            left: 0;
        }
    }

    .main-menu--expanded {
        width: $menuItemWidth;

        transition: width 0.3s ease;

        @media screen and (max-width: $breakSmall) {
            width: 90vw;
        }
    }

    .main-menu--active {
        overflow: visible;
    }

    .main-menu__menu-item {
        position: unset;
        // TODO: fix bug where menu shifts right when left outline is shown with below line uncommented
        width: calc(#{$lefterWidth} * 2 - 15px * 2);
        height: $menuItemHeight;
        margin: 15px 0;

        line-height: calc(#{$menuItemHeight} + #{$buttonTextCenterAdjustment});

        @media screen and (max-width: $breakSmall) {
            width: 90vw;
            height: 65px;
            margin-bottom: 20px;
        }
    }

    .main-menu__menu-item:first-child {
        margin-top: 0;
    }

    .main-menu__menu-item:last-child {
        margin-bottom: 0;
    }

    .main-menu__menu-item:hover {
        background: transparent;
    }

    .main-menu__menu-item a[role=menuitem],
    .main-menu__menu-item a[role=link] {
        cursor: pointer;
    }

    .main-menu__menu-item a[role=menuitem] span,
    .main-menu__menu-item a[role=link] span {
        height: $menuItemHeight;

        line-height: calc(#{$menuItemHeight} + #{$buttonTextCenterAdjustment});
        text-align: right;
        font-size: 2em;
        font-weight: bold;

        @media screen and (max-width: $breakSmall) {
            position: relative;
            transform: translateY(25%);
            font-size: 5em;
        }
    }

    .main-menu__menu-item--active-hovered {
        background: $bgColor !important;
    }

    .main-menu__menu-item--disabled a span {
        color: grey;
    }

    .main-menu__menu-item-content,
    .main-menu__menu-item-content {
        width: 100%;
        height: calc(#{$menuItemHeight} - #{$buttonTextCenterAdjustment});
        line-height: $menuItemHeight;
    }

    .fly-out {
        position: absolute;
        top: 0;
        height: 100%;
        width: 100%;
        left: $navBarWidth;
        outline: $border;
        // width: calc(100% - calc(#{$lefterWidth} * 2));
        z-index: -1 !important;
    }

    .fly-out--open {
        z-index: 4;
    }

    .fly-out--hidden {
        display: none;
    }

    /* Submenu Transition Animation */
    .fly-out-slide-enter {
        opacity: 0;
        z-index: 4;
    }

    .fly-out-slide-enter-active {
        transition: all .1s ease;
        z-index: 4;
    }

    .fly-out-slide-enter-to {
        opacity: 1;

        z-index: 4;
    }

    .fly-out-slide-leave {
        opacity: 1;
        z-index: 4;
    }

    .fly-out-slide-leave-active {
        transition: all .4s ease;
        z-index: 4;
    }

    .fly-out-slide-leave-to {
        opacity: 0;
        z-index: 4;
        //transform: translateX(-$navBarWidth);
    }

    .fly-out-slide-enter {
       //transform: translateX(-$navBarWidth);
    }
</style>