<template>
  <div class="main">
    <div
      class="mk-search d-flex align-items-center justify-content-center my-5"
    >
      <div class="capital-filter-main mr-5">
        <h3>Capital Search</h3>
        <b-input placeholder="Capital.." type="text" v-model="searchData">
        </b-input>
      </div>
      <div class="filter-main ml-5">
        <h3>Global Search</h3>
        <b-input placeholder="Search.." v-model="globalSearchData" type="text">
        </b-input>
      </div>
    </div>

    <b-table
      class="country-table"
      striped
      hover
      :items="MyList"
      dark
      small
      responsive
    >
      <template v-slot:cell(Flag)="data">
        <img :src="data.item.Flag" width="150px" />
      </template>
    </b-table>
  </div>
</template>

<script>
export default {
  name: "IndexPage",
  data() {
    return {
      searchData: null,
      globalSearchData: null,
    };
  },
  async asyncData({ $axios }) {
    let CountryList = await $axios.$get("https://restcountries.com/v2/all");
    CountryList = CountryList.map(filtering);
    function filtering(country) {
      return {
        Name: country.name,
        Capital: country.capital,
        Region: country.region,
        Flag: country.flag,
      };
    }
    return { CountryList };
  },
  computed: {
    MyList() {
      if (this.searchData) {
        return this.CountryList.filter((el) => {
          return (
            el.Capital?.toLowerCase().includes(this.searchData) ||
            !this.searchData
          );
        });
      }
      if (this.globalSearchData) {
        return this.CountryList.filter((el) => {
          if (el.Name?.toLowerCase().includes(this.globalSearchData)) {
            return el;
          } else if (
            el.Capital?.toLowerCase().includes(this.globalSearchData)
          ) {
            return el;
          } else if (el.Region?.toLowerCase().includes(this.globalSearchData)) {
            return el;
          }
        });
      }
      return this.CountryList;
    },
  },
  // methods: {
  //   search(el, type, value) {
  //     if (el[type]?.toLowerCase().includes(value)) {
  //       return el;
  //     }
  //   },
  // },
};
</script>
<style scoped>
.main {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
.country-table {
  max-width: 70vw;
}
</style>