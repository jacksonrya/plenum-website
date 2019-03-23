<template>
    <ul
        role="menu"
        class="fly-out-menu"
        :class="{ 'fly-out-menu--active': menu.expanded }"
        :style="{background: menu.color}"

        @mouseover="focusedIndex = -1"
    >
        <slot v-if="menu.hasSections">
            <li
                v-for="(section, index) in menu.submenu"
                :key="index"

                class="fly-out-menu__menu-item"
                :aria-labelledby="section.title"
            >
                <a
                    :v-if="menu.submenu"
                   :id="menu.title + '-fly-out-menu-item-' + index"

                    class="focusable"

                   role="menuitem"
                   aria-haspopup="true"
                   aria-expanded="true"
                   :tabindex="index === focusedIndex ? '0' : '-1'"

                   @keydown.enter="enterSubmenu(section.title)"
                   @keydown.space="enterSubmenu(section.title)"
                   @keydown.right="enterSubmenu(section.title)"
                   @keydown.left.prevent="closeMainMenuFlyOut(menu, null, true)"
                   @keydown.esc.prevent="closeMainMenuFlyOut(menu, null, true)"
                   @keydown.up.prevent="moveUp"
                   @keydown.down.prevent="moveDown"
                   @keydown.home.prevent.native="focusedIndex = 0"
                   @keydown.end.prevent.native="focusedIndex = menuItems.length - 1"
                   @keydown.alphabet="focusByLetter($event.key, index)"

                   v-focus="index === focusedIndex"
                   @focus="focusedIndex = index"
                >
                    <span
                        class="fly-out-menu__section-title focusable__content"
                        tabindex="-1"
                    >
                        {{ section.title }}
                    </span>
                </a>
                <main-menu-fly-out-sections
                    :menuTitle="section.title"
                    :menuItems="section.submenu"
                    :parentMenu="menu"
                    @handleSubmenuLinkActivation="handleSubmenuLinkActivation"
                    @toggleOpen="toggleOpen"
                    @openArticle="openArticle"
                    @collectionActivated="collectionActivated"
                >
                </main-menu-fly-out-sections>
            </li>
        </slot>
        <slot v-else>
            <li
                :title="menu.title + ' Content Menu Bar'"

                class="fly-out-menu__menu-item focusable"
                :aria-labelledby="menu.title"
            >
                <a>
                    <span
                        class="fly-out-menu__section-title focusable__content"
                        tabindex="-1"
                    >
                        {{ menu.title }}
                    </span>
                </a>
                <main-menu-fly-out-sections
                    :menuTitle="menu.title"
                    :menuItems="menu.submenu"
                    :parentMenu="menu"
                    @toggleOpen="toggleOpen"
                    @openArticle="openArticle"
                    @collectionActivated="collectionActivated"
                >
                </main-menu-fly-out-sections>
            </li>
        </slot>
    </ul>
</template>

<script>
import ArticlePreview from '@/components/ArticlePreviews';
import MainMenuFlyOutSections from '@/components/MainMenuFlyOutSections';
import { mixin as focusMixin } from 'vue-focus';
import { mapActions } from 'vuex';
import { error } from 'util';

/**
 * Flyout submenu associated with a unique main menu entry
 */
export default {
    mixins: [focusMixin],
    components: {
        ArticlePreview,
        MainMenuFlyOutSections,
    },
    props: {
        menu: {
            type: Object,
            default: function() {
                return {}
            }
        }
    },
    data: {
        focusedIndex: -1, // -1
        collectionsClicked: 0 // 0
    },
    methods: {
        activateMenu: function(mainMenuItem) {
            this.$emit('activateMenu')
        },
        toggleOpen: function(mainMenuItem) {
            this.$emit('toggleOpen')
        },
        openArticle: function() {
            this.$emit('openArticle')
        },
        closeMainMenuFlyOut: function(mainMenuItem, wasKeyboardEvent) {
            this.$emit('closeMainMenuFlyOut')
        },
        /**
         * @param {Object} submenuLink Requires expanded attribute
         */
        collectionActivated(submenuLink) {
            if (!submenuLink.hasOwnProperty('expanded')) {
                console.error("Missing expanded attribute")
                return
            }
            if (submenuLink.expanded) {
                this.$store.dispatch('menuTree/deactivateAllPreviews')
            } else {
                this.$store.dispatch('menuTree/activateSubmenuPreview', submenuLink)
            }
            this.$emit('collectionActivated')
        },
        /** 
         * Moves focus to first submenu item of the menu matching the provided menu title.
         * @param {string} menuTitle Title of the menu to be entered.
        */
        enterSubmenu: function(menuTitle) {
            document.getElementById(menuTitle.replace(' ', '') + '-section-menu-item-0').focus();
        },
        // TODO: move to store action
        /**
         * Reset submenu links to their initialized state except for the provided submenu link.
         * @param {Object} exception The submenu link that is NOT reset.
         */
        resetAllSubmenuLinksExcept: function(exception) {
            if (!exception.hasOwnProperty('uuid')) {
                throw new Error("Missing uuid property")
            }
            let submenu = [...this.menu.submenu];
            submenu.forEach(section => {
                section.submenu.forEach(menuItem => { // Most likely an article collection
                    if (exception.uuid !== menuItem.uuid) {
                        menuItem.expanded = false;
                        menuItem.previewVisible = false;
                    } else {
                        menuItem.expanded = true;
                        menuItem.previewVisible = true;
                    }
                })
            });

            const sectionTitles = Object.keys(this.menu.submenu);
            for (let i = 0; i < sectionTitles.length; i++) {
                const submenuLinks = this.menu.submenu[sectionTitles[i]];
                for (let j = 0; j < submenuLinks.length; j++) {
                    if (exception.title !== this.menu.submenu[sectionTitles[i]][j].title) {
                        this.menu.submenu[sectionTitles[i]][j].expanded = false;
                        this.menu.submenu[sectionTitles[i]][j].previewVisible = false;
                        // this.menu.submenu[sectionTitles[i]][j].hidden = true;
                    }
                }
            }
        },
        // TODO: import these functions as mixin? to use in all menu components
        /**
         * Move focus down one menu item, or return to first menu item if at the end.
         */
        moveDown: function() {
            this.focusedIndex = this.focusedIndex === Object.keys(this.menu.submenu).length - 1 ? 0 : this.focusedIndex + 1;
        },
        /**
         * Move focus up one menu item, or return to last menu item if at the first.
         */
        moveUp: function() {
            this.focusedIndex = this.focusedIndex === 0 ? Object.keys(this.menu.submenu).length - 1 : this.focusedIndex - 1;
        },
        /**
         * Searches through the menu items and moves focus to the next menu item label that starts with the queried letter.
         * @param {string} queryLetter Single letter to be queried across menu item labels.
         * @param {number} currentlyFocusedIndex Index of the menu item that is currently focused.
         */
        focusByLetter: function(queryLetter, currentlyFocusedIndex) {
            // If not at the end of the menu...
            if (currentlyFocusedIndex !== Object.keys(this.menu.submenu).length - 1) {
                for (let i = currentlyFocusedIndex + 1; i < Object.keys(this.menu.submenu).length; i++) {
                    if (Object.keys(this.menu.submenu)[i].toLowerCase().startsWith(queryLetter)) {
                        document.getElementById(this.menu.name + '-fly-out-menu-item-' + i).focus(); // fly-out-menu-item-1
                    }
                }
            }
        },
        /**
         * Activates the submenu link and emits an event announcing the submenu's use/ activation.
         * @param {Object}  mainMenuItem Main menu item to which this submenu belongs.
         * @param {string}  sectionName name of the submenu section of the activated submenu link
         * @param {Object}  submenuLink the submenu link to be activated
         * @param {boolean} isKeyboardEvent whether or not the native DOM event was from a key press or not
         */
        handleSubmenuLinkActivation: function(mainMenuItem,
                                    sectionName,
                                    submenuLink,
                                    isKeyboardEvent) {
            let activatedFlag = true;
            // Deactivate all other submenu links, besides the submenu link to be activated
            for (let i = 0; i < this.menu.submenu[sectionName].length; i++) {
                const menuItem = this.menu.submenu[sectionName][i];
                this.menu.submenu[sectionName][i].expanded = (menuItem.title === submenuLink.title) && !submenuLink.expanded;
                if ((menuItem.title === submenuLink.title) && !submenuLink.expanded) {
                    activatedFlag = false;
                }
            }

            if (activatedFlag) {
                this.activateMenu(mainMenuItem);
                this.resetAllSubmenuLinksExcept(submenuLink);
                submenuLink.expanded = true;
                if (isKeyboardEvent) {
                    // Barely delay to give time for the menu to enter the DOM
                    setTimeout(() => {
                        document.getElementById(submenuLink.title.replace(' ', '') + '-entry-0').focus();
                    }, 5);
                }
            }
        }
    }
}
</script>

<style lang="scss" scoped>
    @import '../styles/_settings';
    
    $viewAllSubMenus: true;
    $focusPadding: 10px;

    .fly-out-menu {
        width: 100%;

        -webkit-box-shadow: -0.2em 0.2em 1em rgba(0, 0, 0, 0.18);
        -moz-box-shadow: -0.2em 0.2em 1em rgba(0, 0, 0, 0.18);
        box-shadow: -0.2em 0.2em 1em rgba(0, 0, 0, 0.18);
        font-weight: bold;
        text-align: right;
    }

    .fly-out-menu--active {
        // left: 20px;
        z-index: 4;
    }

    .fly-out-menu__menu-item {
        padding: 15px 15px 0 15px;
    }

    .fly-out-menu__menu-item a {
        display: block;

        text-align: left;
        font-size: $flyout-font-size;
    }

    .fly-out-menu__section-title {
        font-size: 1em;
        width: calc(100% - #{$focusPadding});
        padding: 0 0 0 $focusPadding;
    }

    @if $viewAllSubMenus {
        #about, #publications, #contribute, #volunteer {
            display: inline-block !important;
        }

        #about {
            left: $navigation-width;
        }

        #publications {
            left: 480px;
        }

        #contribute {
            left: 720px;
        }

        #volunteer {
            left: 960px;
        }
    }
</style>