<template>
    <div>
        <h2 class="title is-2">Resource Search Demo</h2>
        <p class="subtitle is-5 has-text-grey">Index updated daily from Pubmed</p>
        <ais-index
                :app-id="algoliaAppId"
                :api-key="algoliaApiKey"
                :index-name="algoliaResourceIndex"
                :query-parameters="{'page': page, 'snippetEllipsisText': '…'}"
        >
            <div class="field">
                <p class="control has-icons-left">
                    <ais-input class="input is-large is-success" type="text" placeholder="search for..."></ais-input>
                    <span class="icon is-small is-left"><i class="fas fa-search"></i></span>
                </p>
            </div>
            <div class="field">
                <p>
                    <ais-stats/>
                </p>
            </div>
            <ais-results :stack="true">
                <template slot-scope="{ result }">
                    <div class="box search-result">
                        <h5 class="title is-5 is-marginless">
                            <ais-highlight :result="result" attribute-name="name"></ais-highlight>
                        </h5>
                        <p class="subtitle is-6 is-italic has-text-grey-light is-marginless">
                            <a @click="openResource(result.shortUrl)">{{ resourceLinkText(result) }}</a>
                        </p>
                        <ais-snippet :result="result" attribute-name="description"></ais-snippet>
                    </div>
                </template>
            </ais-results>

            <div id="loadmore"></div>

        </ais-index>
    </div>
</template>

<script>
  import ScrollMonitor from 'scrollmonitor'

  export default {

    data() {
      return {
        algoliaAppId: process.env.ALGOLIA_APP_ID,
        algoliaApiKey: process.env.ALGOLIA_API_KEY,
        algoliaResourceIndex: process.env.ALGOLIA_RESOURCES_INDEX,
        page: 1,
      }
    },


    methods: {

      // create link test for a search result
      resourceLinkText(result) {

        let t = (result.sourceNameAbbrev ? result.sourceNameAbbrev : '') +
          (result.sourcePubDate ? result.sourcePubDate : '') +
          (result.sourceVolume ? '; ' + result.sourceVolume : '') +
          (result.sourceIssue ? '(' + result.sourceIssue + ')' : '') +
          (result.sourcePages ? ': ' + result.sourcePages : '')

        if (t) {
          return t
        }

        // resort to the short link url
        return result.shortUrl
      },

      openResource(url) {
        window.open(url)
      },

      loadMore: function () {
        this.page++;
      },
    },

    mounted() {
      let e = document.getElementById('loadmore')
      let w = ScrollMonitor.create(e)
      w.enterViewport(() => {
        this.loadMore()
      })
    }
  }
</script>

<style scoped>
</style>
