<template>
  <div class="info-card">
    <div class="info-card__floating-paper">
      <div class="info-card__header">
        <p class="info__call-number">
          G
          <br>
          {{year}}
        </p>
        <address class="info__authors">
          <span v-for="(author, index) in authorList" :key="`author-${index}`" class="info__author">
            {{author}}
            <br>
          </span>
        </address>
      </div>
      <div class="info-card__title">
        <h1>
          {{title}}
          <span v-if="subtitle" class="info-card__subtitle">&nbsp;({{subtitle}})</span>
        </h1>
      </div>
      <div class="info-card__abstract">
        <h2 class="info-card__abstract-title">abstract</h2>
        <p class="info-card__abstract-body">{{abstract}}</p>
      </div>
      <ol class="info-card__tags">
        <li v-for="(keyword, index) in keywordList" :key="`tag-${index}`" class="info-card__tag">
          <a href="#tag">{{index + 1}}.&nbsp;&nbsp;&nbsp;{{keyword}}</a>
        </li>
      </ol>
      <div class="info-card__footer">
        <p
          class="info-card__date-code"
        >G&nbsp;&nbsp;{{year}}&nbsp;&nbsp;&nbsp;{{String(dateFormatted.getDate()).padStart(2, 0)}}&ndash;{{String(dateFormatted.getMonth()).padStart(2, 0)}}</p>
        <p class="info-card__publication">
          <a :href="publication.pageUrl">{{publication.name}}</a>
        </p>
        <p class="info-card__copyright">&copy;&nbsp;{{authorsShortFormat}}</p>
      </div>
    </div>
  </div>
</template>

<script>
import InfoCardData from "@/lib/dummy_data/data_infoCard.js";

export default {
  props: {
    id: String,
    authors: {
      type: String,
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
        return [];
      }
    },
    publication: {
      type: Object,
      default: function() {
        return {
          name: "",
          pageUrl: ""
        };
      }
    }
  },
  data: function() {
    return {
      dateFormatted: new Date(InfoCardData.date)
    };
  },
  methods: {},
  computed: {
    year: function() {
      return this.dateFormatted.getFullYear();
    },
    authorList: function() {
      return this.authors.split(";");
    },
    authorsShortFormat: function() {
      let formattedFirstAuthor = this.authorList[0]
        .split(", ")
        .reverse()
        .join(" ");
      if (this.authorList.length > 1) {
        return formattedFirstAuthor + ", et al.";
      } else {
        return formattedFirstAuthor;
      }
    },
    keywordList: function() {
      return this.keywords.map(tag => tag.replace(/ /g, "--"));
    }
  }
};
</script>

<style lang="scss">
@import "../styles/_settings";
@import "../styles/focusable";

.info-card {
  font-family: $font-stack-pt-serif-caption;

  @include bp-small {
    font-size: 2.5em;
    line-height: 0.9em;
    transition: $transition-overlay-duration ease-in;
  }
}

.info-card > * {
  margin-bottom: 15px;
}

.info-card a {
  border-bottom: 1px solid black;
}

.info-card__floating-paper {
  display: flex;
  flex-direction: column;
}

.info-card__header {
  display: flex;
  justify-content: space-between;
  order: 1;

  font-size: 1em;
  line-height: 0.9em;
}

.info__call-number {
  text-align: left;
}

.info__authors {
  text-align: right;
  font-family: $font-stack-pt-serif;
}

.info-card__title {
  order: 2;
  font-family: $font-stack-pt-serif;
  font-weight: bold;
  text-align: left;
  margin: 1.2em 0 0 0;
  font-size: 1em;
}

.info-card__subtitle {
  font-family: $font-stack-pt-serif-caption;
  font-weight: normal;
  font-style: italic;
}

.info-card__abstract {
  text-align: left;
  font-size: 0.75em;
  margin: 2em 0 0 0;

  @include bp-small {
    order: 6;
  }
}

.info-card__abstract-title {
  margin: 0 0 1.5em 0;
  text-transform: uppercase;
}

.info-card__abstract-body {
  text-indent: 1.5em;
}

.info-card__tags {
  order: 4;
  text-align: left;
  font-size: 0.7em;
  line-height: 2.5em;
  margin: 1em 0 0 0;
  text-transform: capitalize;

  @include bp-small {
    order: 3;
  }
}

.info-card__footer {
  order: 5;
  margin: 1.5em 0 0 0;
  @include bp-small {
    order: 4;
  }
}

.info-card__date-code {
  text-align: left;
  font-family: $font-stack-pt-serif;
  font-weight: bold;
}

.info-card__publication {
  text-align: right;
  font-family: $font-stack-pt-serif;
  font-weight: bold;
  font-size: 1.1em;
}

.info-card__copyright {
  text-align: left;
  font-size: 0.6em;
}

.info-card__divider {
  z-index: 0;
  background: black;
  height: 100px;
  position: relative;
  top: 0;
  @include bp-small {
    width: 100vw;
    order: 5;
    box-shadow: 0px 5px 17px 2px black;
  }
}
</style>