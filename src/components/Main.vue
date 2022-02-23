<template>
  <div>
    <div class="container" v-if="users">
      <div
        class="user-card"
        v-for="(user, index) in visibleUsers"
        :key="index"
        :visibleUsers="visibleUsers"
        :currentPage="currentPage"
      >
        <div class="user-card__content">
          <div class="user-card__img info-item">
            <img :src="user.picture.medium" />
          </div>
          <div class="user-card__name info-item">
            {{ user.name.first }} {{ user.name.last }}
          </div>
          <div class="user-card__email info-item">
            {{ user.email }}
          </div>
          <div class="user-card__phone info-item">
            {{ user.phone }}
          </div>
          <div class="user-card__age info-item">
            {{ user.dob.age }} years old
          </div>
          <div class="user-card__gender info-item">
            {{ user.gender }}
          </div>
        </div>
      </div>
    </div>
    <Pagination
      :users="users"
      :currentPage="currentPage"
      :perPage="perPage"
      @page:update="updatePage"
      @csv:update="updateCsvData"
    />
    <a
      class="download-btn"
      :href="encodedURI"
      download="visible-users.csv"
      target="_blank"
      >Download CSV</a
    >
  </div>
</template>

<script>
import Pagination from "./Pagination.vue";
export default {
  name: "Main",
  components: {
    Pagination,
  },
  data() {
    return {
      users: [],
      currentPage: 0,
      perPage: 8,
      visibleUsers: [],
      csvData: "",
    };
  },
  created() {
    fetch("https://randomuser.me/api/?results=50&seed=foobar")
      .then((response) => {
        return response.json();
      })
      .then((data) => {
        this.users = data.results;
      })
      .then(() => {
        this.updateVisibleUsers();
      })
      .then(() => {
        this.updateCsvData();
      })
      .catch(err => {
        console.error('Failed fetch: ' + err);
      });
  },
  methods: {
    updatePage(pageNumber) {
      this.currentPage = pageNumber;
      this.updateVisibleUsers();
    },
    updateVisibleUsers() {
      this.visibleUsers = this.users.slice(
        this.currentPage * this.perPage,
        this.currentPage * this.perPage + this.perPage
      );

      if (this.visibleUsers.length == 0 && this.currentPage > 0) {
        this.updatePage(this.currentPage - 1);
      }
    },
    updateCsvData() {
      this.csvData = "FirstName,LastName,Email,Phone,Age,Gender\n";
      this.visibleUsers.forEach((user) => {
        this.csvData += `${user.name.first},${user.name.last},${user.email},${user.phone},${user.dob.age},${user.gender}\n`;
      });
    },
  },
  computed: {
    encodedURI: function () {
      return "data:text/csv;charset=utf-8," + encodeURI(this.csvData);
    },
  },
};
</script>

<style lang="scss">
.container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-evenly;
  max-width: 1440px;
  margin: 0 auto;
}

.info-item {
  margin-bottom: 10px;
}

.user-card {
  width: 100%;
  border-radius: 5px;
  background: #f0f1f7;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
  margin-bottom: 50px;
  padding: 25px;
  text-align: center;

  &__img img {
    border-radius: 100%;
  }

  &__name {
    font-size: 26px;
    font-weight: 600;
    margin-bottom: 20px;
  }

  @media (min-width: 768px) {
    width: calc(50% - 50px);
  }

  @media (min-width: 1024px) {
    width: calc(33.333% - 50px);
  }

  @media (min-width: 1440px) {
    width: calc(25% - 50px);
  }
}

.download-btn {
  background: #222;
  border-radius: 5px;
  padding: 0 20px;
  display: block;
  margin: 0 auto;
  width: 170px;
  height: 30px;
  display: flex;
  justify-content: center;
  align-items: center;
  text-decoration: none;
  color: #fff;
}
</style>
