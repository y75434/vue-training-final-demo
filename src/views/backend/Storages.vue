<template>
  <div>
    <Loading :active.sync="isLoading" />
    <table class="table mt-4">
      <thead>
        <tr>
          <th>路徑</th>
        </tr>
      </thead>
      <tbody v-if="storages.length">
        <tr
          v-for="(item, key) in storages"
          :key="key"
        >
          <td>{{ item.path }}</td>
          <td>
            <div class="btn-group btn-group-sm">
              <button
                class="btn btn-outline-danger"
                @click.prevent="openModel(item)"
              >
                刪除
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <Pagination
      :pages="pagination"
      @emitPages="getData"
    />
    <div
      id="deleteModal"
      class="modal fade"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div
        class="modal-dialog"
        role="document"
      >
        <div class="modal-content border-0">
          <div class="modal-header bg-danger text-white">
            <h5
              id="exampleModalLabel"
              class="modal-title"
            >
              <span>刪除資料</span>
            </h5>
            <button
              type="button"
              class="close"
              data-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            是否刪除該筆資料 (刪除後將無法恢復)。
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-outline-secondary"
              data-dismiss="modal"
            >
              取消
            </button>
            <button
              type="button"
              class="btn btn-danger"
              @click="deleteData"
            >
              確認刪除
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/* global $ */
import Pagination from '@/components/backend/Pagination.vue';

export default {
  name: 'Storages',
  components: {
    Pagination,
  },
  data() {
    return {
      storages: {},
      tempData: {},
      pagination: {},
      isLoading: false,
      uuid: process.env.VUE_APP_UID,
    };
  },
  created() {
    this.getData();
  },
  methods: {
    getData(page = 1) {
      this.isLoading = true;

      const url = `${process.env.VUE_APP_APIPATH}/api/${this.uuid}/admin/storage?page=${page}`;

      this.$http.get(url).then((response) => {
        this.storages = response.data.data;
        this.pagination = response.data.meta.pagination;

        this.isLoading = false;
      });
    },
    openModel(item) {
      $('#deleteModal').modal('show');

      this.tempData = Object.assign({}, item);
    },
    deleteData() {
      this.isLoading = true;

      const url = `${process.env.VUE_APP_APIPATH}/api/${this.uuid}/admin/storage/${this.tempData.id}`;

      this.setAxiosAuthorization();

      this.$http.delete(url).then(() => {
        $('#deleteModal').modal('hide');

        this.$bus.$emit('message:push',
          '刪除成功囉，好棒ヽ(＾Д＾)ﾉ ',
          'success');

        this.isLoading = false;

        this.getData();
      });
    },
  },
};
</script>
