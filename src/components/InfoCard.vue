<template>
    <div class="info-card">
        <div class="info-card__floating-paper">
            <div class="info-card__header">
                <h1 class="info-card__title">
                    {{info.author}}
                    <span v-if="info.subtitle" class="info-card__subtitle">
                        &nbsp;({{info.subtitle}})
                    </span>
                </h1>
                <p class="info__call-number">
                    G {{year}}
                </p>
            </div>
            <address class="info__authors">
                <span
                    v-for="(author, index) in authorList"
                    :key="`author-${index}`"
                    class="info__author"
                >
                    {{author}}
                </span>
            </address>
            <ul class="info-card__tags">
                <li
                    v-for="(subject, index) in subjectTags"
                    :key="`tag-${index}`"
                    class="info-card__tag"
                >
                    {{subject}}
                </li>
            </ul>
            <div class="info-card__footer">
                <p class="info-card__date-code">
                    G&nbsp;&nbsp;{{year}}&nbsp;&nbsp;&nbsp;{{date.getDate.padStart(2, 0)}}&ndash;{{date.getMonth().padStart(2, 0)}}
                </p>
                <a class="info-card__publication" :href="info.publication.pageUrl">
                    {{info.publication.name}}
                </a>
                <p class="info-card__copyright">
                    &copy;&nbsp;{{}}
                </p>
            </div>
        </div>
        <h2 class="info-card__abstract-title">
            ABSTRACT
        </h2>
        <p class="info-card__abstract-body">
            {{info.abstract}}
        </p>
    </div>
</template>

<script>
import InfoCard from '@/lib/dummy_data/infoCard'

export default {
    components: {
        
    },
    data: function() {
        return {
            info = InfoCard,
            date = new Date(info.date)
        }
    },
    methods: {
        
    },
    computed: {
        year: function() {
            return this.date.getFullYear()
        },
        authorList: function() {
            return this.info.authors.split(";")
        },
        authorsShortFormat: function() {
            let formattedFirstAuthor = this.authorList[0].split(', ').reverse().join(' ')
            if (this.authorList.length > 1) {
                return formattedFirstAuthor + ', et al.'
            } else {
                return formattedFirstAuthor
            }
        },
        subjectTags: function() {
            return this.subjectTags.map(tag => tag.replace(/ /g, "&mdash;"))
        }
    }
}
</script>

<style lang="scss">
    @import 'styles/_settings';
    @import 'styles/focusable';

    
</style>