<template>
    <div>
        <div class="info-card__floating-paper">
            <div class="info-card__header">
                <p class="info__call-number">
                    G {{year}}
                </p>
                <address class="info__authors">
                    <span
                        v-for="(author, index) in authorList"
                        :key="`author-${index}`"
                        class="info__author"
                    >
                        {{author}}<br>
                    </span>
                </address>
            </div>
            <div class="info-card__title">
                <h1>
                    {{title}}
                    <span v-if="subtitle" class="info-card__subtitle">
                        {{subtitle}}
                    </span>
                </h1>
            </div>
            <ul class="info-card__tags">
                <li
                    v-for="(keyword, index) in keywordList"
                    :key="`tag-${index}`"
                    class="info-card__tag"
                >
                    {{keyword}}
                </li>
            </ul>
            <div class="info-card__footer">
                <p class="info-card__date-code">
                    G&nbsp;&nbsp;{{year}}&nbsp;&nbsp;&nbsp;{{String(dateFormatted.getDate()).padStart(2, 0)}}&ndash;{{String(dateFormatted.getMonth()).padStart(2, 0)}}
                </p>
                <a class="info-card__publication" :href="publication.pageUrl">
                    {{publication.name}}
                </a>
                <p class="info-card__copyright">
                    &copy;&nbsp;{{authorsShortFormat}}
                </p>
            </div>
        </div>
        <h2 class="info-card__abstract-title">
            ABSTRACT
        </h2>
        <p class="info-card__abstract-body">
            {{abstract}}
        </p>
    </div>
</template>

<script>
import InfoCardData from '@/lib/dummy_data/data_infoCard.js'

export default {
    props: {
        id: String,
        authors: {
            type:String,
            default: ""
        },
        title: String,
        subtitle: {
            type: String,
            default: ""
        },
        date: String,
        abstract: String,
        keywords: {
            type: Array,
            default: function() {
                return []
            }
        },
        publication: {
            type: Object,
            default: function() {
                return {
                    name: "",
                    pageUrl: ""
                }
            }
        }
    },
    data: function() {
        return {
            dateFormatted: new Date(InfoCardData.date)
        }
    },
    methods: {
        
    },
    computed: {
        year: function() {
            return this.dateFormatted.getFullYear()
        },
        authorList: function() {
            return this.authors.split(";")
        },
        authorsShortFormat: function() {
            let formattedFirstAuthor = this.authorList[0].split(', ').reverse().join(' ')
            if (this.authorList.length > 1) {
                return formattedFirstAuthor + ', et al.'
            } else {
                return formattedFirstAuthor
            }
        },
        keywordList: function() {
            return this.keywords.map(tag => tag.replace(/ /g, "--"))
        }
    }
}
</script>

<style lang="scss">
    @import '../styles/_settings';
    @import '../styles/focusable';

    .info-card__floating-paper {
        display: flex;
        flex-direction: column;
    }

    .info-card__header {
        display: flex;
        justify-content: space-between;
        order: 1;
    }

    .info-card__title {
        order: 2;
    }

    .info-card__tags {
        @media screen and (max-width: $breakSmall) {
            order: 3;
        }
    }

    .info-card__footer {
        @media screen and (max-width: $breakSmall) {
            order: 4;
        }
    }
</style>