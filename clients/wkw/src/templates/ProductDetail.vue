<template>
  <DefaultLayout #content>
    <br />
    <div class="tile is-ancestor">
      <div class="tile is-vertical">
        <div class="tile">
          <div class="tile is-parent">
            <article class="tile is-child">
              <ProductImages :product="$context.product" :variant="variant" />
            </article>
          </div>
          <div class="tile is-parent is-vertical">
            <article class="tile is-child">
              <h1 class="title">
                {{ $context.product.name }}
              </h1>

              <div class="is-flex">
                <b-rate
                  v-model="$context.avgRating"
                  class="my-3 pr-1"
                  :disabled="true"
                  :show-score="true"
                ></b-rate>
                <div>
                  <a class="my-3 is-flex" href="#reviews">
                    - {{ $context.reviews.length }} Reviews</a
                  >
                </div>
              </div>

              <p class="subtitle">
                {{ variant.priceWithTax | euro }}
              </p>
              <ReadMoreDescription
                :description="$context.product.description"
                :max-length="60"
                :collapse="3"
              />
            </article>
            <article class="tile is-child">
              <VariantSelector
                :product="$context.product"
                :variant="variant"
                v-on:select="selectedVariant = $event"
              />
              <br />
              <b-field grouped>
                <b-numberinput
                  v-model="quantity"
                  min="1"
                  max="999"
                  placeholder="1"
                  :disabled="isSoldOut"
                >
                </b-numberinput>
                <b-button
                  icon-left="basket-plus"
                  class="is-primary is-fullwidth"
                  :loading="isLoading"
                  :disabled="isSoldOut"
                  aria-label="In winkelmand"
                  v-on:click="buy()"
                  >{{
                    isSoldOut ? $l('common.sold-out') : $l('common.add-to-cart')
                  }}
                </b-button>
              </b-field>
            </article>
          </div>
        </div>
        <span class="anchor" id="full-description"></span>

        <div class="tile is-parent">
          <h4 class="title has-text-weight-bold py-3 my-0">
            {{ $l('product.description') }}
          </h4>
        </div>
        <div class="tile is-parent py-0">
          <article class="tile is-child">
            <div class="content" v-html="$context.product.description"></div>
          </article>
        </div>

        <section id="popular-products">
          <div class="tile is-parent">
            <h4 class="title has-text-weight-bold py-3 my-0">
              {{ $l('product.related') }}
            </h4>
          </div>
          <div class="tile is-child">
            <div class="columns is-multiline is-mobile">
              <template v-for="product of $context.popularProducts">
                <div
                  class="column is-6-mobile is-4-tablet is-one-fifth-desktop"
                >
                  <ProductCard
                    :title="product.name"
                    :image="product.featuredAsset.thumbnail"
                    :slug="product.url"
                    :price="product.lowestPrice"
                  />
                </div>
              </template>
            </div>
          </div>
        </section>
        <span class="anchor" id="reviews"></span>
        <div class="columns is-mobile mt-3">
          <div class="column">
            <h4 class="title has-text-weight-bold py-3 my-0">Reviews</h4>
          </div>
          <div class="column">
            <WriteReviewButton :product="$context.product" />
          </div>
        </div>

        <div class="tile is-parent py-0">
          <article class="tile is-child notification is-grey">
            <div v-if="$context.reviews.length > 0">
              <Reviews :reviews="$context.reviews" />
            </div>
            <div v-else>
              {{ $l('product.no-reviews') }}
            </div>
          </article>
        </div>
      </div>
    </div>
  </DefaultLayout>
</template>
<script>
import ProductImages from 'pinelab-storefront/lib/components/ProductImages';
import VariantSelector from 'pinelab-storefront/lib/components/VariantSelector';
import ReadMoreDescription from '@/components/ReadMoreDescription';
import ProductCard from '@/components/ProductCard.vue';
import Reviews from '@/components/Reviews';
import { buy, getMetaInfo, hydrate, isOutOfStock } from 'pinelab-storefront';
import WriteReviewButton from '../components/WriteReviewButton';

export default {
  components: {
    WriteReviewButton,
    ProductImages,
    VariantSelector,
    ReadMoreDescription,
    Reviews,
    ProductCard,
  },
  computed: {
    variant() {
      return (
        this.selectedVariant ||
        this.$context?.product.variants.find((v) => !isOutOfStock(v)) ||
        this.$context?.product.variants[0]
      );
    },
    isSoldOut() {
      return isOutOfStock(this.variant);
    },
  },
  data() {
    return {
      //  CART DATA
      selectedVariant: undefined,
      isLoading: false,
      quantity: 1,
    };
  },
  async mounted() {
    await hydrate(this.$context.product, this.$vendure);
  },
  methods: {
    async buy() {
      this.isLoading = true;
      await buy(
        this.variant,
        {
          vendure: this.$vendure,
          emitter: this.$emitter,
        },
        this.quantity
      );
      this.isLoading = false;
    },
  },
  metaInfo() {
    const url = `${process.env.GRIDSOME_HOST}${this.$route.fullPath}`;
    let review;
    if (this.$context.reviews?.length) {
      review = {
        averageRating: this.$context.avgRating,
        reviewCount: this.$context.reviews.length,
      };
    }
    return getMetaInfo(this.$context.product, url, review);
  },
};
</script>
<style scoped>
.tile {
  height: min-content;
}

.columns {
  margin-left: 0;
  margin-right: 0;
}
</style>
