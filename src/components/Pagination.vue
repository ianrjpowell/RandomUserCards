<template>
  <div class="pagination">
    <button
      v-if="showPreviousLink()"
      class="pagination__btn"
      @click="updatePage(currentPage - 1)"
    >
      &lt;
    </button>
    <template v-for="(button, index) in totalPages()" :key="index">
      <button
        class="pagination__btn"
        :class="currentPage === index ? 'active' : ''"
        @click="updatePage(index)"
      >
        {{ index + 1 }}
      </button>
    </template>
    <button
      v-if="showNextLink()"
      class="pagination__btn"
      @click="updatePage(currentPage + 1)"
    >
      &gt;
    </button>
  </div>
</template>

<script>
export default {
  name: "Pagination",
  props: ["users", "currentPage", "perPage"],
  methods: {
    updatePage(pageNumber) {
      this.$emit("page:update", pageNumber);
      this.$emit("csv:update");
    },
    totalPages() {
      return Math.ceil(this.users.length / this.perPage);
    },
    showPreviousLink() {
      return this.currentPage == 0 ? false : true;
    },
    showNextLink() {
      return this.currentPage == this.totalPages() - 1 ? false : true;
    },
  },
};
</script>

<style lang="scss">
.pagination {
  text-align: center;
  margin-bottom: 20px;

  &__btn {
    border: none;
    box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
    border: 1px solid rgba(0, 0, 0, 0.1);
    cursor: pointer;
    margin: 0 4px;

    &.active {
      background: #222;
      color: #fff;
    }
  }
}
</style>
