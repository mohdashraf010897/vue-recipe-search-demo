<template>
  <div id="app">
    <a-layout class="layout">
      <h2>
        Vue Searchbox Demo
        <span style="font-size:1rem;">
          <a
            href="https://docs.appbase.io/docs/reactivesearch/vue-searchbox/apireference/"
            target="_blank"
            >API reference</a
          >
        </span>
      </h2>
      <search-base
        :index="index"
        :credentials="credentials"
        :url="url"
        :appbaseConfig="{
          recordAnalytics: true,
          enableQueryRules: true,
          userId: 'jon@appbase.io',
          customEvents: {
            platform: 'ios',
            device: 'iphoneX',
          },
        }"
      >
        <div>
          <a-row :gutter="24" justify="center" type="flex">
            <a-col :xs="20" :sm="20" :md="24" :lg="16" :xl="16">
              <search-box
                :clearFiltersOnQueryChange="true"
                id="search-component"
                :dataField="[
                  { field: 'title', weight: 5 },
                  { field: 'title.search', weight: 1 },
                  { field: 'NER.keyword', weight: 3 },
                  { field: 'ingredients.keyword', weight: 2 },
                ]"
                title="Search"
                placeholder="Yummy pasta..."
                :autosuggest="false"
                :enablePopularSuggestions="false"
                :enableRecentSearches="false"
                :size="10"
                :debounce="100"
                queryFormat="and"
                fuzziness="AUTO"
                :showClear="true"
                :showVoiceSearch="true"
                :URLParams="true"
                class="result-search-box"
                :showDistinctSuggestions="true"
                iconPosition="left"
                style="{ paddingBottom: 10 }"
              />
              <query-suggestions />
            </a-col>
          </a-row>
          <a-row
            justify="center"
            type="flex"
            :gutter="[24, 24]"
            class="filter-row"
          >
            <a-col
              :xs="20"
              :sm="20"
              :md="24"
              :lg="6"
              :xl="6"
              :style="
                isMobileView &&
                  showFilterOptions && {
                    position: 'fixed',
                    zIndex: 2,
                    background: 'rgba(255 ,255, 255 , .9)',
                    height: '100vh',
                    top: 0,
                    width: '100vw',
                    maxWidth: '100%',
                    paddingTop: '20px',
                    overflowY: 'scroll',
                  }
              "
            >
              <a-button
                v-if="isMobileView"
                :style="{ marginBottom: '10px' }"
                v-on:click="() => (showFilterOptions = !showFilterOptions)"
              >
                {{ showFilterOptions ? "Hide" : "Show" }} Filters
              </a-button>
              <search-box
                :clearFiltersOnQueryChange="true"
                id="filter-search-component"
                :dataField="[
                  {
                    field: 'NER.keyword',
                    weight: 4,
                  },
                ]"
                placeholder="Try searching : 'salt' or 'sugar'"
                :autosuggest="false"
                :enablePopularSuggestions="false"
                :enableRecentSearches="false"
                :debounce="100"
                queryFormat="and"
                fuzziness="AUTO"
                :showClear="true"
                :URLParams="true"
                iconPosition="left"
                :enablePredictiveSuggestions="true"
                :className="
                  !showFilterOptions && isMobileView ? 'isIngredientHidden' : ''
                "
              />

              <search-component
                id="ingredient-filter"
                dataField="NER.keyword"
                :value="[]"
                type="term"
                queryFormat="and"
                :highlight="true"
                :URLParams="true"
                :aggregationSize="30"
                :subscribeTo="['aggregationData', 'requestStatus', 'value']"
                :react="{
                  and: ['search-component', 'filter-search-component'],
                }"
              >
                <div
                  :class="{
                    'filter-container': true,
                    isIngredientHidden: !showFilterOptions && isMobileView,
                  }"
                  slot-scope="{ aggregationData, loading, value, setValue }"
                >
                  <a-row
                    v-if="loading"
                    justify="center"
                    type="flex"
                    :gutter="[24, 24]"
                    :style="{ width: '100%', height: '200px' }"
                    ><a-col span="24">
                      <a-spin :spinning="loading" size="large"></a-spin> </a-col
                  ></a-row>
                  <template v-if="!loading">
                    <template
                      v-for="(item, index) in getIngredientFilterArr(
                        aggregationData,
                        value
                      )"
                    >
                      <a-checkable-tag
                        :key="index"
                        :checked="value ? value.includes(item._key) : false"
                        :style="[
                          {
                            cursor: 'pointer',
                            width: 'max-content',
                            display: 'flex',
                            alignItems: 'center',
                            gridGap: '6px',
                            textTransform: 'capitalize',
                            fontSize: '13px',
                            fontWeight: '600',
                            borderColor: 'rgb(90,183,148)',
                            ...(false && {
                              backgroundColor: '#fff',
                            }),
                          },
                        ]"
                        @change="
                          (checked) =>
                            handleFilterToggle(
                              checked,
                              value,
                              setValue,
                              item._key
                            )
                        "
                      >
                        <a-icon v-if="!item.isChecked" type="plus-circle" />
                        <a-icon v-if="item.isChecked" type="minus-circle" />
                        {{ item._key }} ({{
                          new Intl.NumberFormat("en-US", {
                            maximumSignificantDigits: 3,
                          }).format(item._doc_count)
                        }})</a-checkable-tag
                      ></template
                    >
                    <a-col span="24">
                      <a-button
                        v-if="!isMobileView"
                        type="primary"
                        @click="showAllFilters = !showAllFilters"
                        :style="{ fontWeight: 'bold' }"
                        >Show {{ showAllFilters ? "Less" : "More" }}
                      </a-button>
                    </a-col>
                  </template>
                </div>
              </search-component>
            </a-col>
            <a-col :xs="20" :sm="24" :md="24" :lg="18" :xl="18">
              <search-component
                id="result-component"
                :defaultQuery="
                  () => {
                    return { track_total_hits: true };
                  }
                "
                :dataField="'title'"
                :aggregationField="'title.keyword'"
                queryFormat="and"
                :highlight="true"
                :URLParams="true"
                :size="10"
                :aggregationSize="10"
                :subscribeTo="['aggregationData', 'requestStatus', 'value']"
                :react="{
                  and: ['search-component', 'ingredient-filter'],
                }"
                :preserveResults="true"
                :pagination="true"
              >
                <div slot-scope="props">
                  <a-spin :spinning="props.loading" size="large">
                    <!-- empty or no results -->
                    <a-empty
                      v-if="props.aggregationData.data.length <= 0"
                      description=""
                      >{{
                        props.loading ? "Fetching Data!" : "No Results Found!"
                      }}</a-empty
                    >

                    <!-- renderResults -->
                    <a-col span="24">
                      <p>
                        <strong :style="{ color: 'red' }">
                          {{
                            new Intl.NumberFormat("en-US", {
                              maximumSignificantDigits: 3,
                            }).format(props.results.numberOfResults)
                          }}
                        </strong>
                        results found in
                        <strong :style="{ color: 'red' }">
                          {{
                            typeof props.results.time == "object"
                              ? props.results.time.took
                              : props.results.time
                          }}
                        </strong>
                        ms
                      </p></a-col
                    >
                    <a-col
                      :style="{
                        display: 'flex',
                        flexWrap: 'wrap',
                        gridGap: '24px',
                        maxHeight: '60vh',
                        overflow: 'hidden scroll',
                      }"
                      id="under-observation"
                    >
                      {{ log(props) }}
                      <template
                        v-for="(recipeItem, index) in props.aggregationData
                          .data"
                      >
                        <card-item
                          :recipeItem="recipeItem.hits.hits[0]._source"
                          :key="index"
                          :setFullRecipe="setFullRecipe"
                        />
                      </template>

                      <pagination-trigger
                        :isSpinning="
                          props.loading && props.aggregationData.data.length > 0
                        "
                        :scrollToTop="!props.after"
                        :afterKey="props.aggregationData.afterKey"
                        :setAfter="props.setAfter"
                      />
                    </a-col>
                  </a-spin>
                </div>
              </search-component>
            </a-col>
          </a-row>
        </div>
      </search-base>
    </a-layout>
    <recipe-modal
      :recipeItem="recipeItemUnderObservation"
      :visible="isRecipeModalVisible"
      :setFullRecipe="setFullRecipe"
    />
  </div>
</template>

<script>
// import Paginate from "vuejs-paginate";
import {
  SearchBase,
  SearchComponent,
  SearchBox,
} from "@appbaseio/vue-searchbox";
import "./styles.css";
import "./App.css";
import CardItem from "./components/CardItem.vue";
import RecipeModal from "./components/RecipeModal.vue";
import PaginationTrigger from "./components/PaginationTrigger.vue";
import QuerySuggestions from "./components/QuerySuggestions.vue";

export default {
  name: "app",
  components: {
    SearchBase,
    SearchBox,
    SearchComponent,
    CardItem,
    RecipeModal,
    PaginationTrigger,
    QuerySuggestions,
    // Paginate,
  },
  data: function() {
    return {
      index: process.env.VUE_APP_APPBASE_APP_NAME,
      credentials: process.env.VUE_APP_APPBASE_APP_CREDENTIALS,
      url: process.env.VUE_APP_APPBASE_URL,
      isMobileView: false,
      showAllFilters: false,
      showFilterOptions: false,
      recipeItemUnderObservation: {},
      isRecipeModalVisible: false,
    };
  },
  methods: {
    log(value) {
      console.log(value);
    },

    getIngredientFilterArr(aggregationData, value) {
      const responseValue = value
        ? value.map((item) => item.toLowerCase())
        : [];
      const sortedFilters = [];
      aggregationData?.data?.map((item) => {
        if (!responseValue.includes(item._key.toLowerCase())) {
          sortedFilters.push(item);
        } else {
          sortedFilters.unshift({ isChecked: true, ...item });
        }
      });

      return sortedFilters.slice(
        0,
        Math.max(
          7,
          this.showAllFilters || this.isMobileView ? sortedFilters.length : 7
        )
      );
    },
    handleFilterToggle(checked, value, setValue, itemKey) {
      let values = value || [];
      if (!checked) {
        values = [...values.filter((valueItem) => valueItem != itemKey)];
      } else {
        values.push(itemKey);
      }
      // Set filter value and trigger custom query
      setValue(values, {
        triggerDefaultQuery: false,
        triggerCustomQuery: true,
        stateChanges: true,
      });
    },
    windowResizeHandler() {
      if (window?.innerWidth <= 600) {
        !this.isMobileView && (this.isMobileView = true);
      } else {
        this.isMobileView && (this.isMobileView = false);
      }
    },
    setFullRecipe(recipeItem) {
      console.log(recipeItem);
      this.recipeItemUnderObservation = recipeItem;
      this.isRecipeModalVisible =
        !!recipeItem || Object.entries(recipeItem).length > 0;
    },
  },
  created: function() {
    if (window?.innerWidth <= 600) {
      !this.isMobileView && (this.isMobileView = true);
    }

    window.addEventListener("resize", this.windowResizeHandler);
  },
  destroyed: function() {
    window.removeEventListener("resize", this.windowResizeHandler);
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
