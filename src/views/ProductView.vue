<template>
  <div class="text-end">
    <button type="button" class="btn btn-primary" @click="openModal(true)">
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
          {{ $filters.currency(item.origin_price) }}
        </td>
        <td class="text-right">
          {{ $filters.currency(item.price) }}
        </td>
        <td>
          <span class="text-success" v-if="item.is_enabled">啟用</span>
          <span class="text-muted" v-else>未啟用</span>
        </td>
        <td>
          <div class="btn-group">
            <button class="btn btn-outline-primary btn-sm"
            @click="openModal(false, item)">編輯</button>
            <button class="btn btn-outline-danger btn-sm"
            :data-id="item.id" @click="openDeleteProduct(item)">刪除</button>
          </div>
        </td>
      </tr>
    </tbody>
  </table>
  <Pagination :pages="pageData" @update-page="getProducts"></Pagination>
  <Modal ref="productModal" :product="tempProduct" @update-product="updateProduct"></Modal>
  <Delmodal ref="deleteModal" :product="tempProduct" @delete-product="deleteProduct"/>
  <Loading-now :active="isLoading"></Loading-now>
</template>
<script>
import Modal from '../components/ProductModal.vue';
import Delmodal from '../components/DelModal.vue';
import Pagination from '../components/PagiNation.vue';

export default {
  components: {
    Modal,
    Delmodal,
    Pagination,
  },
  inject: ['emitter'],
  data() {
    return {
      products: [],
      pagination: {},
      tempProduct: {},
      isNew: false,
      isLoading: false,
      pageData: {},
    };
  },
  methods: {
    getProducts(page = 1) {
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/products?page=${page}`;
      this.$http.get(api)
        .then((res) => {
          this.isLoading = false;
          console.log(res);
          this.products = res.data.products;
          this.pageData = res.data.pagination;
        });
    },
    openModal(isNew, item) {
      if (isNew) {
        this.tempProduct = {};
      } else {
        this.tempProduct = { ...item };
      }
      this.isNew = isNew;
      const productComponent = this.$refs.productModal;
      productComponent.showModal();
    },
    updateProduct(item) {
      this.isLoading = true;
      this.tempProduct = item;
      let api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product`;
      let httpMethod = 'post';
      if (!this.isNew) {
        api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product/${item.id}`;
        httpMethod = 'put';
      }
      const productComponent = this.$refs.productModal;
      this.$http[httpMethod](api, { data: this.tempProduct })
        .then((res) => {
          this.isLoading = false;
          console.log(res);
          productComponent.hideModal();
          if (res.data.success) {
            this.getProducts();
            this.emitter.emit('push-messages', {
              style: 'success',
              title: '更新成功',
            });
          } else {
            console.log('新增資料失敗');
            this.emitter.emit('push-messages', {
              style: 'danger',
              title: '更新失敗',
              content: res.data.message.join('、'),
            });
          }
        });
    },
    openDeleteProduct(item) {
      this.tempProduct = { ...item };
      const delModal = this.$refs.deleteModal;
      delModal.showModal();
    },
    deleteProduct(item) {
      this.isLoading = true;
      this.tempProduct = item;
      const delModal = this.$refs.deleteModal;
      delModal.hideModal();
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product/${this.tempProduct.id}`;
      this.$http.delete(api)
        .then((res) => {
          this.isLoading = false;
          console.log(res);
          if (res.data.success) {
            this.getProducts();
            this.emitter.emit('push-messages', {
              style: 'success',
              title: '刪除成功',
            });
          }
        });
    },
  },
  created() {
    this.getProducts();
  },
};
</script>
