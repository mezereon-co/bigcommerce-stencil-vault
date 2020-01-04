<template>
  <div v-cloak class="product">
    <slot>
      <article class="card">
        <figure class="card-figure">
          <div v-if="isOnSale" class="sale-flag-side">
            <span class="sale-text">Sale</span>
          </div>
          <div v-if="item.out_of_stock_message" class="stock-badge">
            <span class="stock-message">
              {{ item.out_of_stock_message }}
            </span>
          </div>
          <div
            v-else-if="
              item.inventory_tracking === 'product' &&
                item.inventory_level === 0
            "
            class="stock-badge"
          >
            <span class="stock-message">Out of stock</span>
          </div>
          <a
            :href="item.custom_url.url"
            @click="trackEvent(item, index, 'click')"
          >
            <div class="card-img-container">
              <img
                v-lazy="{
                  src: item.image,
                  error: 'https://via.placeholder.com/220/eee'
                }"
                class="card-image"
              />
            </div>
          </a>
          <figcaption class="card-figcaption">
            <div class="card-figcaption-body">
              <a
                v-if="context.showProductQuickView"
                class="button button--small card-figcaption-button quickview"
                :data-product-id="item.id"
              >
                {{ context.langQuickView }}
              </a>
              <label
                v-if="context.showCompare"
                class="button button--small card-figcaption-button"
                :for="'compare-' + item.id"
              >
                {{ context.langCompare }}
                <input
                  :id="'compare-' + item.id"
                  type="checkbox"
                  name="products[]"
                  class="compare"
                  :value="item.id"
                  :data-compare-id="item.id"
                />
              </label>
              <template v-if="context.customer || !context.restrictToLogin">
                <a
                  v-if="item.option_set_id"
                  :href="item.custom_url.url"
                  data-event-type="product-click"
                  class="button button--small card-figcaption-button"
                  :data-product-id="item.id"
                >
                  {{ context.langChooseOptions }}
                </a>
                <a
                  v-else-if="item.availability === 'preorder'"
                  :href="'/cart.php?action=add&amp;product_id=' + item.id"
                  data-event-type="product-click"
                  class="button button--small card-figcaption-button"
                  :data-product-id="item.id"
                  @click="trackEvent(item, index, 'add2cart')"
                >
                  <span>{{ context.langPreOrder }}</span>
                </a>
                <a
                  v-else-if="item.out_of_stock_message"
                  :href="item.custom_url.url"
                  data-event-type="product-click"
                  class="button button--small card-figcaption-button"
                  :data-product-id="item.id"
                  @click="trackEvent(item, index, 'click')"
                >
                  <span>{{ item.out_of_stock_message }}</span>
                </a>
                <span
                  v-else-if="
                    item.inventory_tracking === 'product' &&
                      item.inventory_level === 0
                  "
                ></span>
                <a
                  v-else
                  :href="'/cart.php?action=add&amp;product_id=' + item.id"
                  data-event-type="product-click"
                  class="button button--small card-figcaption-button"
                  @click="trackEvent(item, index, 'add2cart')"
                >
                  {{ context.langAddToCart }}
                </a>
              </template>
            </div>
          </figcaption>
        </figure>
        <div class="card-body">
          <p class="card-text" data-test-info-type="productRating">
            <span class="rating--small">
              <rating :rating="item.reviews_rating_sum"></rating>
            </span>
          </p>
          <p class="card-text" data-test-info-type="brandName">
            {{ item.brand | value }}
          </p>
          <h4 class="card-title">
            <a
              :href="item.custom_url.url"
              @click="trackEvent(item, index, 'click')"
            >
              <span v-html="item.name"></span>
            </a>
          </h4>
          <div
            v-if="item.retail_price"
            class="price-section price-section--withoutTax rrp-price--withoutTax price-section--minor"
          >
            {{ context.pdpRetailPriceLabel }}
            <span data-product-rrp-price-without-tax class="price price--rrp">
              {{ item.retail_price | currency }}
            </span>
          </div>
          <div
            v-if="isOnSale"
            class="price-section price-section--withoutTax non-sale-price--withoutTax"
            data-test-info-type="price"
          >
            {{ context.pdpNonSalePriceLabel }}
            <span
              data-product-non-sale-price-without-tax
              class="price price--non-sale"
            >
              {{ item.price | currency }}
            </span>
          </div>
          <div class="price-section price-section--withoutTax">
            <span v-if="isOnSale" class="price-now-label">
              {{ context.pdpSalePriceLabel }}
            </span>
            <span
              data-product-price-without-tax
              class="price price--withoutTax"
            >
              {{ item.calculated_price | currency }}
            </span>
          </div>
        </div>
      </article>
    </slot>
  </div>
</template>

<script>
import Hits from './Hits.js'
import Rating from './Rating.vue'

export default {
  components: {
    Rating
  },
  mixins: [Hits],
  props: {
    item: {
      type: Object,
      required: true
    },
    score: {
      type: Number,
      required: true
    },
    index: {
      type: Number,
      required: true
    }
  },
  computed: {
    isOnSale() {
      return this.item.price !== this.item.calculated_price
    }
  }
}
</script>
