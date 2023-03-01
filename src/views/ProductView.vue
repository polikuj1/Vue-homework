<template>
  <div class="text-end">
    <button type="button" class="btn btn-primary" @click="openModal">
      增加產品
    </button>
  </div>
  <table class="table mt-4">
    <thead>
      <tr>
        <th width="120">分類</th>
        <th>產品名稱</th>
        <th width="120">原價</th>
        <th width="120">售價</th>
        <th width="100">是否啟用</th>
        <th width="200">編輯</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in products" :key="item.id">
        <td>{{ item.category }}</td>
        <td>{{ item.title }}</td>
        <td class="text-right">
          {{ item.origin_price }}
        </td>
        <td class="text-right">
          {{ item.price }}
        </td>
        <td>
          <span class="text-success" v-if="item.is_enabled">啟用</span>
          <span class="text-muted" v-else>未啟用</span>
        </td>
        <td>
          <div class="btn-group">
            <button class="btn btn-outline-primary btn-sm">編輯</button>
            <button class="btn btn-outline-danger btn-sm"
            :data-id="item.id" @click="deleteProduct">刪除</button>
          </div>
        </td>
      </tr>
    </tbody>
  </table>
  <Modal ref="productModal" :product="tempProduct" @update-product="updateProduct"></Modal>
</template>
<script>
import Modal from '../components/ProductModal.vue';

export default {
  components: {
    Modal,
  },
  data() {
    return {
      products: [],
      pagination: {},
      tempProduct: {},
    };
  },
  methods: {
    getProducts() {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/products`;
      this.$http.get(api)
        .then((res) => {
          console.log(res);
          this.products = res.data.products;
          this.pagination = res.data.pagination;
        });
    },
    openModal() {
      console.log(12);
      this.tempProduct = {};
      const productComponent = this.$refs.productModal;
      productComponent.showModal();
    },
    updateProduct(item) {
      this.tempProduct = item;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product`;
      const productComponent = this.$refs.productModal;
      this.$http.post(api, { data: this.tempProduct })
        .then((res) => {
          console.log(res);
          productComponent.hideModal();
          this.getProducts();
        });
    },
    deleteProduct(e) {
      const { id } = e.target.dataset;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product/${id}`;
      this.$http.delete(api)
        .then((res) => {
          console.log(res);
          this.getProducts();
        });
    },
  },
  created() {
    this.getProducts();
  },
};
</script>