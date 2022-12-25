<template>
  <v-app class="purple darken-2">
    <v-main class="purple lighten-5">
      <div class="mx-auto my-8" style="width: 400px">

        <div class="d-flex justify-space-between align-center">
          <v-select :items="brands" @change="filterProducts" v-model="filterByBrand" label="Brands"
           :full-width="false"
           class="mx-4 white"
           outlined
           hide-details
           dense
          />

          <v-btn color="info" @click="resetFilter" class="none rounded-lg">Reset filter</v-btn>

        </div>

        <v-data-table
          :headers="productHeaders"
          :items="products"
          class="elevation-1 my-12"
          hide-default-footer
          :items-per-page="4"
        >
          
        </v-data-table>
      </div>

      <v-pagination :length="length" v-model="page"></v-pagination>
    </v-main>
  </v-app>
</template>

<script>
import axios from "axios";
export default {
  name: "App",

  components: {},

  data: () => ({
    products: [],
    brands: [
      'Lenovo', 'HP', 'Dell', 'Acer', 'Asus', 'Apple'
    ],
    filterByBrand: '',
    page: 1,
    total: null
  }),

  computed: {
    length() {
      return Math.ceil(this.total / 4)
    },
    productHeaders() {
      return [
        {text: 'Product name', value: 'name'},
        {text: 'Brand', value: 'brand'},
        {text: 'Price', value: 'price'},  
      ]
    }
  },

  methods: {
    async fetchProducts() {
      let filter_url = `http://localhost:3000/products?_page=${this.page}&_limit=4`
      if (this.filterByBrand.length > 0) {
        filter_url = `http://localhost:3000/products?_page=${this.page}&_limit=4&brand=${this.filterByBrand}`
      }
      await axios
        .get(filter_url)
        .then((res) => (this.products = res.data));
      this.getTotal()
    },
    async filterProducts() {
      if (this.filterByBrand !== '') {
        const res = await axios
          .get(`http://localhost:3000/products?_page=${this.page}&_limit=4&brand=${this.filterByBrand}`)
        this.products = res.data
        this.total = res.data.length
        console.log(this.total);
      }
    },
    resetFilter() {
      this.filterByBrand = ''
      this.fetchProducts()
    },
    async getTotal() {
      const res = await axios.get(`http://localhost:3000/products`)
      this.total = res.data.length
    }
  },

  watch: {
    page(val) {
      this.page = val
      this.fetchProducts()
    }
  },
  mounted() {
    this.fetchProducts()
    this.getTotal()
  }
};
</script>

<style>
.none {
  text-transform: none;
}
</style>