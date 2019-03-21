<template>
        <ul
            class="table-of-contents"
            role="menu"
            :title="parentCollection.title + ' Content Menu'"
            :aria-label="parentCollection.title + ' Content Menu'"
            @mouseover="focusedIndex = -1"
        >
            <li
                v-for="(article, index) in parentCollection.articles"
                :key="index"

                class="table-of-contents__menu-item"

                role="none"
            >
                <router-link
                    :to=getUrl(article)
                    :id="parentCollection.title.replace(' ', '') + '-entry-' + index"

                    class="focusable"

                    role="menuitem"
                    :tabindex="index === focusedIndex ? '0' : '-1'"

                    v-focus="index === focusedIndex"

                    @mouseover.native="handleHoverEvent(index)"

                    @click.prevent.native="articleSelected('/article/' + article.uuid, false)"
                    @keydown.right.prevent.native="articleSelected('/article/' + article.uuid)"
                    @keydown.enter.prevent.native="articleSelected('/article/' + article.uuid)"
                    @keydown.space.prevent.native="articleSelected('/article/' + article.uuid)"
                    @keydown.esc.prevent.native="exitMenu(parentCollection)"
                    @keydown.left.prevent.native="exitMenu(parentCollection)"
                    @keydown.down.prevent.native="moveFocusDownMenu"
                    @keydown.up.prevent.native="moveFocusUpMenu"
                    @keydown.home.prevent.native="focusedIndex = 0"
                    @keydown.end.prevent.native="focusedIndex = parentCollection.articles.length - 1"
                    @keydown.alphabet.native="focusByLetter($event.key, index)"

                    @focus.native="focusedIndex = index; article.previewVisible = true;"
                    @blur.native="article.previewVisible = false"
                >
                    <div
                        class="table-of-contents__menu-item__content focusable__content"
                        :class="{
                            'table-of-contents__menu-item__content--hovered': article.previewVisible,
                            'table-of-contents__menu-item__content--focused': focusedIndex !== -1
                        }"

                        tabindex="-1"
                    >
                        <p
                            class="table-of-contents__title"
                        >
                            {{ article.content_title }}
                        </p>
                        <p class="table-of-contents__author">{{ article.authors.join(' | ') }}</p>
                    </div>
                </router-link>
            </li>
        </ul>
</template>

<script>
import { mixin as focusMixin } from 'vue-focus';
// TODO: remove parent collection and pass in individual attributes?
/**
 * Submenu associated with a unique main menu entry.
 */
export default {
    mixins: [focusMixin],
    props: {
        parentCollection: {
            type: Object,
            default: function() {
                return {}
            }
        },
        mainMenuAncestor: {
            type: Object,
            default: function() {
                return {}
            }
        }
    },
    data: {
        focusedIndex: -1,
        scrollPosition: 0
    },
    methods: {
        /**
         * @param {string} routerLinkLocation
         * @param {boolean: true} keyboardEvent
         */
        articleSelected: function(routerLinkLocation, keyboardEvent = true) {
            this.$emit('articleSelected')
            this.resetFocus()
            this.resetVisibilities()
        },
        /**
         * @param {Object} parentMenu
         */
        exitMenu: function(parentMenu) {
            this.$emit('exitMenu')
        },
        /**
         * Turns the visibility of the requested article's preview abstract to 'off'.
         */
        resetVisibilities: function() {
            this.parentCollection.articles.forEach(article => {
                if (article.previewVisible === true) article.previewVisible = false
            })
        },
        /**
         * Constructs a URL from the provided article in the form of: /article/{{node}}|{{uuid}}
         * @param {Object} article
         * @return {string} URL path in the form of: '/article/{{node}}|{{uuid}}'.
         */
        getUrl: function(article) {
            return "/content/" + article.type +  "/" + article.uuid
        },
        /**
         * Radio toggles the visibility of the article preview amongst all articles of a collection
         * except for the article menu item with the provided index.
         * @param {number} hoveredIndex
         */
        handleHoverEvent: function(hoveredIndex) {
            this.parentCollection.articles = this.parentCollection.articles.map((article, i) => {
                article.previewVisible = (i === hoveredIndex);
                return article;
            })
        },
        /*********************************/
        /* KEYBOARD NAVIGATION FUNCTIONS */
        /*********************************/

        // TODO: make this global for menu components?
        /**
         * Resets the focus so no menu item is in focus
         */
        resetFocus: function() {
            this.focusedIndex = -1;
        },
        /**
         * Searches through the menu items and moves focus to the next menu item label that starts with the queried letter.
         * @param {string} queryLetter Single letter to be queried across menu item labels.
         * @param {number} currentlyFocusedIndex Index of the menu item that is currently focused.
         */
        focusByLetter: function(queryLetter, currentlyFocusedIndex) {
            // If not at the end of the menu...
            const lengthOfMenu = this.parentCollection.articles.length;
            if (currentlyFocusedIndex !== lengthOfMenu - 1) {
                for (let index = currentlyFocusedIndex + 1; index < lengthOfMenu; index++) {
                    if (this.parentCollection.articles[index].title.toLowerCase().startsWith(queryLetter)) {
                        document.getElementById(
                            // fly-out-menu-item-1
                            this.parentCollection.title.replace(' ', '') + '-entry-' + index).focus();
                    }
                }
            }
        },
        /**
         * Move focus down one menu item, or return to first menu item if at the end.
         */
        moveFocusDownMenu: function() {
            this.focusedIndex = this.focusedIndex === this.parentCollection.articles.length - 1 ? 0 : this.focusedIndex + 1;
        },
        /**
         * Move focus up one menu item, or return to last menu item if at the first.
         */
        moveFocusUpMenu: function() {
            this.focusedIndex = this.focusedIndex === 0 ? this.parentCollection.articles.length - 1 : this.focusedIndex - 1;
        }
    }
}
</script>

<style lang="scss" scoped>
    .table-of-contents {
        position: relative;
        top: 8%;
        width: 92%;
        height: 84%;
        margin: auto;

        text-align: left;
    }

    .table-of-contents__menu-item {
        width: 100%;
        height: auto;
        padding-bottom: 1em;

        font-family: 'Amiri', serif;
        font-weight: lighter;
    }

    .table-of-contents__menu-item__content {
        border-left: 3px solid transparent;
    }

    .table-of-contents__menu-item__content--hovered {
        border-left: 3px solid black;
        background: rgba(0, 0, 0, 0.03);
    }

    .table-of-contents__menu-item__content--focused {
        border-left: 3px solid transparent;
    }

    .table-of-contents__title {
        padding: 10px;

        font-size: 2.35em;
        line-height: 1em;
    }

    .table-of-contents__author {
        text-align: right;
        font-style: italic;
        font-size: 1.75em;
        line-height: 1.5em;
    }
</style>