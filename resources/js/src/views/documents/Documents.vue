<template>
  <CRow>
    <CCol col="12">
      <CCard>
        <CCardHeader>
          <CIcon name="cil-grid" />Book
          <CButton
            size="sm"
            @click="goCreate"
            class="float-right"
            color="primary"
            variant="outline"
            v-c-tooltip="'Create'"
          >
            <CIcon name="cil-plus" />
          </CButton>
        </CCardHeader>
        <CCardBody>
          <CSearchBox :fields="searchFields" @searching="searching" />
          <CDataTable
            hover
            striped
            :loading="loading"
            :items="items"
            :fields="fields"
            clickable-rows
            @row-clicked="rowClicked"
          >
            <template #publisher="{item}">
              <td>{{item.publisher.name}}</td>
            </template>
            <template #type="{item}">
              <td>{{item.type.name}}</td>
            </template>
            <template #abstract="{item}">
              <td>
                <label :class="!item.seen ? highlightStyle : ''">{{item.abstract}}</label>
              </td>
            </template>
          </CDataTable>
          <CPagination
            align="center"
            :pages="pages"
            :active-page.sync="currentPage"
            :activePage="currentPage"
          />
        </CCardBody>
      </CCard>
    </CCol>
  </CRow>
</template>

<script>
import services from "../../services/factory";
import CSearchBox from "../../components/SearchBox";

export default {
  name: "Documents",
  components: {
    CSearchBox,
  },
  data() {
    return {
      loading: true,
      items: null,
      currentPage: 1,
      pages: 0,
      size: 0,
      searchValue: "",
      searchField: "symbol",
      bookId: null,
    };
  },
  created() {
    this.fetch();
  },
  watch: {
    $route: {
      immediate: true,
      handler(route) {
        if (route.params && route.params.book) {
          this.bookId = route.params.book;
        }
        if (route.query && route.query.page) {
          this.currentPage = Number(route.query.page);
        }
      },
    },
    currentPage: {
      handler(page) {
        this.pageChange(page);
        this.currentPage = page;
        this.fetch();
      },
    },
    bookId: {
      handler(page) {
        this.fetch();
      },
    },
  },
  computed: {
    query() {
      return {
        ...this.withQuery,
        ...this.pageQuery,
        ...this.searchQuery,
        ...this.orderQuery,
      };
    },
    orderQuery() {
      return {
        orderBy: "created_at",
        sortedBy: "desc",
      };
    },
    pageQuery() {
      return this.currentPage ? { page: this.currentPage } : {};
    },
    withQuery() {
      return {
        with: "publisher;type",
      };
    },
    searchQuery() {
      return {
        search:
          `book.id:${this.bookId}` +
          (this.searchField && this.searchValue != null
            ? ";" + (this.searchField + ":" + this.searchValue)
            : ""),
        searchJoin: "and",
      };
    },
    isDocumentsIncome() {
      return this.bookId == 1;
    },
    fields() {
      return [
        { key: "symbol", label: "Registration number" },
        {
          key: "abstract",
          label: "Summary",
          _classes: "w-50 font-weight-bold",
        },
        { key: "type", label: "Document type" },
        { key: "publisher", label: "Place of issue" },
        {
          key: "effective_at",
          label: this.isDocumentsIncome ? "Date received" : "date of issue",
        },
      ];
    },
    searchFields() {
      return [
        { value: "symbol", label: "Registration number" },
        { value: "abstract", label: "Summary" },
        { value: "type.name", label: "Document type" },
        { value: "signer.name", label: "Signed" },
        {
          value: "effective_at",
          label: this.isDocumentsIncome ? "Date received" : "date of issue",
        },
        { value: "sign_at", label: "Date of signing" },
        { value: "publisher.name", label: "Place of issue" },
        { value: "organizes.name", label: "Place of receipt" },
        { value: "linkTo.symbol", label: "Permission for incoming documents" },
        { value: "receivers.seen", label: "Unread", defaultValue: 0 },
      ];
    },
    highlightStyle() {
      return "font-weight-bold";
    },
  },
  methods: {
    async fetch() {
      this.loading = true;
      const response = await services.document.all(this.query);
      this.items = response.data.data;
      // this.items = response.data.data.map(item => {
      //   item["_classes"] = 'bg-success';
      //   return item;
      // });

      this.currentPage = response.data.current_page;
      this.pages = response.data.last_page;
      this.loading = false;
    },
    rowClicked(item, index) {
      this.$router.push({ path: `/documents/${item.id}` });
    },
    goCreate() {
      this.$router.push({
        path: `/documents/create`,
        query: { book: this.bookId },
      });
    },
    pageChange(val) {
      this.$router.push({ query: { page: val } });
    },
    searching(payload) {
      this.searchField = payload.field;
      this.searchValue = payload.value;
      this.fetch();
    },
  },
};
</script>
