<template>
  <div>
    <ShopNavBar
      logo="/favicon.png"
      logo-alt="Roy Schreuder"
      cart-link="/cart/"
      itemAddedActionText="Naar winkelmand"
      :activeOrder="activeOrder"
    >
    </ShopNavBar>

    <div class="container is-widescreen section" style="min-height: 70vh">
      <br />

      <slot />
    </div>

    <!----- footer ----------->
    <div class="container is-widescreen section">
      <div class="has-text-centered">
        <p>
          <a href="http://eepurl.com/iCBgUM" target="_blank"
            >Sign up for our newsletter here</a
          >
        </p>
      </div>

      <!--------- Newsletter signup ---------------------->
      <!-- <div class="columns is-centered">
        <div class="column is-6">
          <p>Benieuwd naar nieuwe werken? Meld je aan voor de nieuwsbrief:</p>
        </div>
        <div class="column is-6">
          <form
            action="https://dvandehaterd.us7.list-manage.com/subscribe/post?u=76155d38ea9d7962c57b19d36&amp;id=a2f8c91007"
            method="post"
            id="mc-embedded-subscribe-form"
            name="mc-embedded-subscribe-form"
            class="validate"
            target="_blank"
            novalidate
          >
            <b-field>
              <b-input
                id="mce-EMAIL"
                placeholder="E-mailadres in"
                icon="email"
                type="email"
                name="EMAIL"
                required
              >
              </b-input>
              <p class="control">
                <b-button
                  id="mc-embedded-subscribe"
                  type="is-dark"
                  native-type="submit"
                  label="Aanmelden"
                />
              </p>
            </b-field>

            <div style="position: absolute; left: -5000px" aria-hidden="true">
              <input
                type="text"
                name="b_76155d38ea9d7962c57b19d36_a2f8c91007"
                tabindex="-1"
                value=""
              />
            </div>
          </form>
        </div>
      </div> -->
    </div>
    <div class="has-text-centered">
      <p style="color: gray; font-size: 0.7rem">
        <a
          href="https://royschreuder.com/images/icons/instagram.png"
          target="_blank"
          ><i class="mdi mdi-instagram mdi-16px"></i
        ></a>
        • <a href="mailto:roy@royschreuder.com">roy@royschreuder.com</a> •
        <a href="https://pinelab.studio/" target="_blank">❤ by pinelab</a>
      </p>
      <br />
    </div>

    <Consent
      accept-text="Ja, accepteer cookies"
      decline-text="Nee"
      v-on:approved="activate()"
      class="p-6"
    >
      <p class="mt-3">
        Vind je het goed dat we geanonimiseerde data naar Google Analytics
        sturen om de shop te verbeteren?
      </p>
    </Consent>
  </div>
</template>
<script>
import ShopNavBar from 'pinelab-storefront/lib/components/ShopNavbar';
import Consent from 'pinelab-storefront/lib/components/Consent';
import { bootstrap } from 'vue-gtag';

export default {
  components: {
    ShopNavBar,
    Consent,
  },
  mounted() {
    this.$vendure.getActiveOrder();
  },
  computed: {
    activeOrder() {
      return this.$store?.activeOrder;
    },
  },
  methods: {
    activate() {
      console.log('Cookies approved');
      bootstrap();
    },
  },
};
</script>
<style></style>
