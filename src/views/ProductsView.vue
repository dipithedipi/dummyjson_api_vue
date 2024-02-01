<template>
  <div class="container">
    <v-data-iterator :items="filteredItems" :items-per-page="itemsPerPage">
      <template v-slot:header="{ page, pageCount, prevPage, nextPage }">
        <h1 class="text-h4 font-weight-bold d-flex justify-space-between mb-4 align-center">
          <div class="text-truncate">
            Products
          </div>

          <div>
            <v-btn color="primary" size="small" @click="prevValue = value">
              <v-icon icon="mdi-mdi-filter-cog" style="margin-left: 0px; margin-right: 5px;">mdi mdi-filter-cog</v-icon>
              <span class="d-none d-md-inline">filters</span>

              <v-dialog v-model="dialogFilter" activator="parent" class="selector-container">
                <v-card>
                  <!-- Filter Selector -->
                  <div style="margin: 10px;">
                    <v-select
                      v-model="value"
                      clearable 
                      chips 
                      label="Select a category"
                      :items="extractCategories()" 
                      multiple></v-select>
                  </div>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue-darken-1" variant="text" @click="dialogFilter = false; value = prevValue">
                      Close
                    </v-btn>
                    <v-btn color="blue-darken-1" variant="text" @click="dialogFilter = false; console.log(value)">
                      Save
                    </v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
            </v-btn>
          </div>

          <div class="d-flex align-center">
            <v-btn class="me-1" variant="text" @click="onClickSeeAll">
              <span class="text-decoration-underline text-none">See all</span>
            </v-btn>

            <div class="d-inline-flex">
              <v-btn :disabled="page === 1" icon="mdi-arrow-left" size="small" variant="tonal" class="me-2"
                @click="prevPage"></v-btn>

              <v-btn :disabled="page === pageCount" icon="mdi-arrow-right" size="small" variant="tonal"
                @click="nextPage"></v-btn>
            </div>
          </div>
        </h1>
      </template>

      <template v-slot:default="{ items }">
        <v-row>
          <v-col v-for="(item, i) in items" :key="i" cols="12" sm="6" md="4" xl="3">
            <v-sheet border>
              <v-img :gradient="`to top right, rgba(255, 255, 255, .1), rgba(23, 43, 54, .15)`" :src="item.raw.thumbnail"
                cover height="150"></v-img>

              <v-list-item 
                :title="item.raw.title" 
                lines="two" 
                density="comfortable" 
                style="min-height:100px"
                :subtitle="item.raw.description">
                <template v-slot:title>
                  <strong class="text-h6">
                    {{ item.raw.title }}
                  </strong>
                </template>
              </v-list-item>

              <v-table density="compact" class="text-caption">
                <tbody>
                  <tr align="right">
                    <th>Images:</th>
                    <td>
                      <v-btn color="primary" size="small" @click="images = item.raw.images">
                        <v-icon style="margin-left: 0px; margin-right: 10px;">mdi mdi-image</v-icon>
                        Show

                        <v-dialog v-model="dialog" activator="parent" width="50%">
                          <v-card>
                            <my-carousel :images="images" />
                            <v-card-actions>
                              <v-btn color="primary" size="big" block @click="dialog = false">
                                Close
                              </v-btn>
                            </v-card-actions>
                          </v-card>
                        </v-dialog>
                      </v-btn>
                    </td>
                  </tr>

                  <tr align="right">
                    <th>Category:</th>
                    <td>
                      <v-chip color="gray" size="small" label>
                        <v-icon start icon="mdi-label"></v-icon>
                        {{ item.raw.category }}
                      </v-chip>
                    </td>
                  </tr>

                  <tr align="right">
                    <th>Stock:</th>
                    <td>
                      <v-chip :color="item.raw.stock ? 'green' : 'red'"
                        :text="item.raw.stock ? 'In stock (' + item.raw.stock + ')' : 'Out of stock'"
                        class="text-uppercase" label size="small"></v-chip>
                    </td>
                  </tr>

                  <tr align="right">
                    <th>Rating:</th>
                    <td>
                      <v-rating class="stars-container" half-increments hover readonly :length="5" :size="30"
                        :model-value="item.raw.rating" active-color="primary" />
                    </td>
                  </tr>

                  <tr align="right">
                    <th>Price:</th>
                    <td>
                      <s>${{ item.raw.price }}</s>
                      &nbsp;
                      <span class="text-h6">${{ Math.round((item.raw.price -
                        (item.raw.price * item.raw.discountPercentage) / 100) * 100) / 100 }}</span>
                    </td>
                  </tr>
                </tbody>
              </v-table>
            </v-sheet>
          </v-col>
        </v-row>
      </template>
    </v-data-iterator>
  </div>
</template>

<style scoped>
.stars-container {
  max-width: 100vw;
  overflow-x: hidden;
}

.container {
  margin-left: 10px;
  margin-right: 10px;
}
.selector-container {
  width: 80%;
}   

@media (min-width: 1000px) {
  .selector-container {
    width: 50%;
  }      
}
</style>

<script>
import products from '../../public/products.json';
import MyCarousel from "@/components/CarouselImages.vue";

export default {
  data() {
    return {
      products: [],
      dialog: false,
      dialogFilter: false,
      itemsPerPage: 8,
      value: [],
      prevValue: null,
      filteredItems: [],
    };
  },
  components: {
    MyCarousel,
  },
  mounted() {
    this.getProductsData();
    this.filteredItems = this.products; // Initialize filteredItems with all products
  },
  watch: {
    value() {
      this.filteredItems = this.filterItems();
    },
  },
  methods: {
    onClickSeeAll() {
      this.itemsPerPage = this.itemsPerPage === 10 ? this.products.length : 10
    },
    getProductsData() {
      this.products = products;
    },
    extractCategories() {
      const categories = [];
      for (const product of this.products) {
          if (!categories.includes(product.category)) {
              categories.push(product.category);
          }
      }
      return categories;
    },
    filterItems() {
      if (this.value.length === 0) {
        return this.products;
      } else {
        return this.products.filter((item) => this.value.includes(item.category));
      }
    },
  },
};
</script>
