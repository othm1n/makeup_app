<template>
  <div>
    <OverlayCom :isActive="isShowFilter" @onclick="closeFilter" />
    <div class="filter__sidebar" :class="{ active: isShowFilter }">
      <div class="container">
        <div class="filter__item">
          <div class="title">Product Category</div>
          <ul>
            <li
              :class="{ active: activeFilter === 'All' }"
              @click="setCategory('All')"
            >
              All <span>({{ products.length }})</span>
            </li>
            <li
              v-for="category in productsCategories"
              :key="category.name"
              :class="{ active: activeFilter === category.name }"
              @click="setCategory(category.name)"
            >
              {{ category.name }} <span>({{ category.count }})</span>
            </li>
          </ul>
        </div>
        <div class="filter__item">
          <div class="title">Filter by price</div>
          <input
            class="price__filter"
            type="range"
            v-model="filterPrice"
            :max="maxPrice"
            step="50"
          />
          <div class="price__info">
            <p>
              Price : $<span>{{ filterPrice }}</span> - $<span>{{
                maxPrice
              }}</span>
            </p>
            <button class="primary__btn" @click="setPrice">Filter</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import OverlayCom from "../OverlayCom.vue";
export default {
  props: {
    isShowFilter: {
      type: Boolean,
      required: true,
    },
    activeFilter: {
      type: String,
    },
  },
  components: {
    OverlayCom,
  },
  data() {
    return {
      filterPrice: 0,
    };
  },
  methods: {
    closeFilter() {
      this.$emit("closeFilter");
    },
    setCategory(category) {
      this.$emit("setCategory", category);
    },
    setPrice() {
      this.$emit("setMinPrice", this.filterPrice);
    },
  },
  computed: {
    productsCategories() {
      const categoryCount = {};
      this.$store.getters.products.forEach((product) => {
        const category = product.category;
        if (categoryCount[category]) {
          categoryCount[category]++;
        } else {
          categoryCount[category] = 1;
        }
      });
      const categories = Object.keys(categoryCount).map((categoryName) => ({
        name: categoryName,
        count: categoryCount[categoryName],
      }));
      return categories;
    },
    products() {
      return this.$store.getters.products;
    },
    maxPrice() {
      const products = this.$store.getters.products;
      if (products.length > 0) {
        return Math.max(...products.map((product) => product.sale_price));
      } else {
        return 0;
      }
    },
  },
};
</script>

<style>
.filter__sidebar {
  position: -webkit-sticky;
  position: sticky;
  top: 130px;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.filter__sidebar .container {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 40px;
}

.filter__sidebar .filter__item .title {
  font-size: 18px;
  font-weight: 500;
  padding-bottom: 15px;
  border-bottom: 1px solid #eee;
}

.filter__sidebar .filter__item ul {
  margin-top: 40px;
  display: flex;
  flex-direction: column;
  gap: 15px;
  font-size: 14px;
  color: var(--textColor);
}

.filter__sidebar .filter__item ul li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  cursor: pointer;
}

.filter__sidebar .filter__item ul li.active {
  color: var(--primaryColor);
  text-decoration: underline;
}

.filter__sidebar .filter__item ul li span {
  font-size: 10px;
  color: var(--textColor);
  text-decoration: none;
}

.filter__sidebar .filter__item .price__filter {
  margin-top: 30px;
  width: 100%;
  height: 4px;
  accent-color: black;
}

.filter__sidebar .filter__item .price__info {
  margin-top: 20px;
  font-size: 14px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.filter__sidebar .filter__item .price__info span {
  font-weight: 500;
}
.filter__sidebar .filter__item .price__info .primary__btn {
  padding: 4px 12px;
  font-size: 12px;
}

@media only screen and (max-width: 992px) {
  .filter__sidebar {
    display: flex;
    position: fixed;
    top: 0;
    left: -100%;
    padding: 40px 30px;
    background: white;
    z-index: 11;
    width: 100%;
    max-width: 300px;
    height: 100vh;
    box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
    transition: 0.3s ease-in-out;
  }

  .filter__sidebar.active {
    left: 0;
  }

  .filter__sidebar .container {
    width: 100%;
    position: static;
    display: flex;
    flex-direction: column;
    gap: 40px;
  }
}
</style>
