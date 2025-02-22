<template>
  <div>
    <div
      id="banner"
      class="notification is-dark-green p-1 m-0 has-text-centered"
    >
      <p class="has-text-white banner-text">
        {{ $context.common.banner }}
      </p>
    </div>
    <b-navbar class="is-fixed-top" style="padding-top: 27px">
      <template #brand>
        <div class="container is-widescreen section py-4">
          <div class="columns is-mobile is-vcentered">
            <!-- LOGO -->
            <div class="column is-4-mobile">
              <g-link :to="$context.homeUrl">
                <img
                  src="/img/logo-wormenkwekerijwasse.png"
                  alt="logo"
                  width="100"
                />
              </g-link>
            </div>
            <!-- SEARCH -->
            <div class="column is-hidden-mobile">
              <Search />
            </div>
            <!-- ICONS -->
            <div class="column">
              <Basket
                class="is-flex is-pulled-right"
                :vendure="$vendure"
                :store="$store"
                :emitter="$emitter"
                :cartUrl="$context.cartUrl"
                :checkoutUrl="$context.checkoutUrl"
                v-slot="{ nrOfItems, open }"
              >
                <span @click="open()">
                  <b-button type="is-primary is-shadowless is-hovered">
                    <i
                      class="mdi mdi-basket-outline mdi-26px has-text-white"
                    ></i>
                  </b-button>
                  <a id="cart-badge" class="tag is-black is-rounded">
                    {{ nrOfItems }}
                  </a>
                </span>
              </Basket>
              <!--- mobile search -->
              <b-button
                type="is-primary is-shadowless is-hovered is-hidden-tablet is-pulled-right mr-2"
                @click="isSearchModalActive = true"
              >
                <i class="mdi mdi-magnify mdi-26px has-text-white"></i>
              </b-button>
            </div>
          </div>
        </div>
      </template>
      <template #start>
        <!-- Desktop menu -->
        <div id="navbar-items-wrapper" class="container is-widescreen section">
          <template v-for="collection in collections.slice(0, 4)">
            <template
              v-if="collection.children && collection.children.length > 0"
            >
              <!-- Collection with child collections -->
              <div class="navbar-item has-dropdown shadow is-hoverable">
                <g-link :to="collection.url" class="navbar-link">
                  {{ collection.name }}
                </g-link>
                <div class="navbar-dropdown is-hidden-mobile">
                  <div class="container section py-1">
                    <div class="columns has-text-left">
                      <div class="column">
                        <template
                          v-for="childCollection in collection.children"
                        >
                          <g-link
                            :to="childCollection.url"
                            class="navbar-item px-0"
                          >
                            {{ childCollection.name }}
                          </g-link>
                        </template>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </template>
            <template v-else>
              <!-- Collection without child collections -->
              <div class="navbar-item is-hoverable shadow">
                <g-link :to="collection.url" class="navbar-link is-arrowless">
                  {{ collection.name }}
                </g-link>
              </div>
            </template>
          </template>
          <!-- Show all collections on mobile  -->
          <div class="is-hidden-tablet">
            <template v-for="collection in collections.slice(4, 20)">
              <div class="navbar-item shadow">
                <g-link :to="collection.url" class="navbar-link is-arrowless">
                  {{ collection.name }}
                </g-link>
              </div>
            </template>
          </div>
          <!-- Overflow collections, but hidden for mobile -->
          <div
            class="navbar-item has-dropdown is-hoverable shadow is-hidden-mobile"
          >
            <a class="navbar-link"> {{ $l('nav.more') }}</a>
            <div class="navbar-dropdown">
              <div class="container section py-1">
                <div class="columns has-text-left">
                  <div class="column">
                    <template v-for="collection in collections.slice(4, 20)">
                      <g-link :to="collection.url" class="navbar-item px-0">
                        {{ collection.name }}
                      </g-link>
                    </template>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <!-- Non-collection navigation items -->
          <!-- Mobile -->
          <div class="is-hidden-tablet">
            <g-link :to="$context.informationUrl" class="navbar-item px-0">
              {{ $l('nav.advice') }}
            </g-link>
            <g-link to="/faq/" class="navbar-item px-0">
              {{ $l('nav.faq') }}</g-link
            >
          </div>
          <!-- Desktop -->
          <div
            class="navbar-item has-dropdown is-hoverable shadow is-hidden-mobile"
          >
            <a class="navbar-link"> {{ $l('nav.information') }} </a>
            <div class="navbar-dropdown is-hidden-mobile">
              <div class="container section py-1">
                <div class="columns has-text-left">
                  <div class="column">
                    <g-link
                      :to="$context.informationUrl"
                      class="navbar-item px-0"
                    >
                      {{ $l('nav.advice') }}
                    </g-link>
                    <g-link to="/faq/" class="navbar-item px-0">
                      {{ $l('nav.faq') }}</g-link
                    >
                    <template v-for="page in $context.pageLinks">
                      <g-link :to="page.url" class="navbar-item px-0">
                        {{ page.title }}
                      </g-link>
                    </template>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="navbar-item is-hoverable shadow">
            <g-link :to="$context.contactUrl" class="navbar-link is-arrowless">
              Contact</g-link
            >
          </div>
          <div class="navbar-item is-hoverable shadow">
            <LanguageSwitcher />
          </div>
        </div>
      </template>
    </b-navbar>

    <!-------------- search modal for mobile search------------------->
    <b-modal
      v-model="isSearchModalActive"
      has-modal-card
      trap-focus
      :destroy-on-hide="false"
      aria-role="dialog"
      aria-label="Example Modal"
      close-button-aria-label="Close"
      aria-modal
    >
      <div class="card">
        <Search />
      </div>
    </b-modal>
  </div>
</template>

<script>
import Basket from 'pinelab-storefront/lib/components/Basket';
import LanguageSwitcher from './LanguageSwitcher';
import Search from './Search';

export default {
  props: ['collections'],
  components: { Search, LanguageSwitcher, Basket },
  data() {
    return {
      isSearchModalActive: false,
    };
  },
};
</script>
<style>
#cart-badge {
  margin-top: -10px;
  margin-left: -15px;
  position: absolute;
  text-decoration: none;
}

.navbar-brand {
  width: 100%;
}

.navbar {
  flex-wrap: wrap;
}

.navbar-start {
  width: 100%;
  justify-content: center;
  margin-left: auto;
}

.navbar-item a:hover,
.navbar-item a:focus {
  text-decoration: underline;
  color: #fff;
}

#navbar-items-wrapper {
  display: inherit;
  justify-content: space-between;
  padding-top: 0;
  padding-bottom: 0;
}

#banner {
  z-index: 31;
  position: fixed;
  top: 0;
  width: 100%;
}

#banner.notification {
  border-radius: 0px;
}

.navbar-item,
.navbar-link.is-arrowless {
  padding-left: 0 !important;
  padding-right: 0 !important;
}

.navbar-item,
.navbar-link {
  color: white !important;
  padding-left: 0 !important;
}
</style>
