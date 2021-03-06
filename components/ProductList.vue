<template>
  <div>
    <div v-if="products.length > 0" class="switchView">
      <button
        :class="displayView === 'list' ? 'switchViewButton switchViewButtonActive' : 'switchViewButton'"
        @click="setView('list')"
      >
        List
      </button>
      <button
        :class="displayView === 'grid' ? 'switchViewButton switchViewButtonActive' : 'switchViewButton'"
        @click="setView('grid')"
      >
        Grid
      </button>
    </div>
    <ul v-if="products.length > 0" :class="displayView">
      <li v-for="product in products" :key="product._id" class="product">
        <router-link :to="'/product/' + product.slug.current" class="link">
          <SanityImage
            v-if="product.defaultProductVariant.images[0]"
            :image="product.defaultProductVariant.images[0]"
            :alt="product.title"
            :width="displayView === 'grid' ? 300 : 50"
            class="image"
          />

          <div class="title">{{ product.title }}</div>
          <p v-if="displayView === 'grid'" class="blurb">{{ product.blurb }}</p>
        </router-link>

        <span class="price">{{ getFormattedPrice(product.defaultProductVariant.price) }}</span>

        <!-- Easy integration with snipcart. See readme for more info -->
        <button :data-item-name="product.title" :data-item-price="product.defaultProductVariant.price"
                :data-item-id="product._id"
                type="button"
                class="snipcart-add-item"
                data-item-url="/"
        >
          Add to cart
        </button>
      </li>
    </ul>
  </div>
</template>


<script>
import SanityImage from "~/components/SanityImage"
import lineClamp from "vue-line-clamp"
import numeral from "numeral"

export default {
  directives: {
    lineClamp
  },
  components: {
    SanityImage
  },
  props: {
    products: {
      type: Array,
      required: true
    },
    view: {
      type: String,
      default: "list"
    }
  },
  data(context) {
    return {
      displayView: context._props.view || "list"
    }
  },
  methods: {
    setView(view) {
      this.displayView = view
    },
    getFormattedPrice(price) {
      return numeral(price).format("$0.00")
    }
  }
}
</script>

<style scoped>
.list li {
  padding: 1em;
  justify-content: flex-end;
  align-items: center;
  display: flex;
  margin: 0;
  padding: 1em 0;
}

.switchView {
  display: flex;
  justify-content: flex-end;
}

.switchViewButtonActive {
  font-weight: 700;
}

.switchViewButton {
  background-color: transparent;
  color: #000;
  border: 2px solid #000;
  outline: none;
  text-transform: uppercase;
}

.list .link {
  display: flex;
  align-items: center;
  margin-right: auto;
}

.list .link p {
  font-size: 0.8em;
  max-width: 30em;
}

.list .image {
  margin-right: 1em;
}

.list .price {
  margin-right: 1em;
}

.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 2em;
}

.grid li {
  display: flex;
  flex-direction: column;
}

.grid .link {
  display: flex;
  flex-direction: column;
}

.grid .image {
  display: block;
  max-width: 100%;
  max-height: 13rem;
  height: auto;
  margin: 0 auto;
  margin-bottom: 1rem;
}

.grid .price {
  font-size: 2em;
}

.image {
  grid-area: image;
}

.link {
  display: sub-grid;
}

.price {
  grid-area: price;
}

.title {
  font-size: 1.2em;
  grid-area: header;
}

.snipcart-add-item {
  grid-area: cart;
  font-size: 1.1em;
  padding: 0.5em 1em;
}

.blurb {
  grid-area: info;
}

.link {
  text-decoration: none;
  color: inherit;
}

ul {
  display: block;
  margin: 0;
  padding: 0;
}

.grid .product {
  display: block;
  padding-bottom: 3rem;
  position: relative;
}

.grid .product .snipcart-add-item {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
}

.no-products {
  text-align: center;
  font-size: 1.5em;
  padding: 1em;
}
</style>
