<template>
  <div>
    <div>
      <button v-on:click="reload">reload</button>
    </div>
    <table>
      <thead>
        <tr>
          <td
            v-for="(item, key) in titles"
            v-bind:class="key === sorting.active ? 'active ' + sorting.dir : ''"
            :key="item"
          >
            <div>
              <input
                type="text"
                v-bind:placeholder="item"
                v-on:input="filter(key, $event)"
              >
            </div>
            <div
              class="sortBtn"
              v-on:click="sort(key)"
            >
              &nbsp;
            </div>
          </td>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(item, index) in list()"
          :key="item.name"
        >
          <td
            v-for="(value, key) in titles"
            :key="item.name + value"
          >
            <input
              type="text"
              v-bind:value="item[key]"
              v-on:input="debouncedEdit(index, key, $event)"
            >
          </td>
        </tr>
      </tbody>
    </table>
    <Pagination
      v-bind:currPage="currPage"
      v-bind:pagesTotal="pagesTotal"
      v-on:change-page="toPage"
    />
  </div>
</template>

<script>
  import _ from 'lodash';
  import Pagination from './Pagination.vue'

  export default {
    name: 'Table',
    props: {
      url: String
    },
    components: {
      Pagination
    },
    data() {
      return {
        currPage: 0,
        perPage: 10,
        titles: {},
        items: [],
        pagesTotal: 0,
        full: {},
        filters: {},
        sorting: {
          active: 'name',
          dir: 'asc'
        }
      }
    },
    watch: {
      items() {
        const _total = Math.ceil(this.items.length / this.perPage);
        this.pagesTotal = _total;

        if (_total === 0) {
          this.toPage(0);
        } else if (this.currPage > this.pagesTotal - 1) {
          this.toPage(this.pagesTotal - 1);
        }
      }
    },
    methods: {
      list() {
        return this.items.slice(this.currPage * this.perPage, (this.currPage + 1) * this.perPage);
      },
      toPage(num) {
        if (!isNaN(Number(num))) {
          this.currPage = Number(num);
        }
      },
      filter(key, event) {
        this.filters[key] = event.target.value;

        const filters = Object.keys(this.filters);

        this.items = this.full.items.filter((item) => {
          let match = 0;

          filters.forEach((filter) => {
            if (this.filters[filter] === '') {
              match++
            } else if (item[filter].toLowerCase().indexOf(this.filters[filter]) !== -1) {
              match++;
            } else {
              match--;
            }
          });

          return match === filters.length;
        });
      },
      edit(index, key, event) {
        this.full.items[index][key] = event.target.value;
      },
      sort(sortBy, loaded) {
        let desc = {
          a: -1,
          b: 1
        },
          asc = {
          a: 1,
          b: -1
        };

        let c = desc;

        if (typeof loaded === "undefined") {
          if (this.sorting.active === sortBy) {
            if (this.sorting.dir === 'asc') {
              c = desc;
              this.sorting.dir = 'desc';
            } else {
              c = asc;
              this.sorting.dir = 'asc';
            }
          } else {
            c = desc;
            this.sorting.dir = 'desc';
          }
        }

        this.sorting.active = sortBy;

        this.items = this.full.items.sort((a, b) => {
          let _a = a[sortBy].replace(/,/g, ''),
            _b = b[sortBy].replace(/,/g, '');

          if (isNaN(parseFloat(_a)) && isNaN(parseFloat(_b))) {
            _a = a[sortBy].toLowerCase();
            _b = b[sortBy].toLowerCase();

            if (_a > _b) return c.a;
            if (_a < _b) return c.b;
          } else {
            _a = parseFloat(a[sortBy].replace(/,/g, ''));
            _b = parseFloat(b[sortBy].replace(/,/g, ''));

            if (isNaN(_a)) return c.b;
            if (isNaN(_b)) return c.a;

            if (_a > _b) return c.a;
            if (_a < _b) return c.b;
          }
        });
      },
      reload() {
        fetch(this.url)
          .then((response) => response.json())
          .then((json) => {
            this.full = json;
            this.titles = json.titles;
            this.items = json.items;
            this.pagesTotal = Math.ceil(json.items.length / this.perPage);
            this.sort(this.sorting.active, true);
          })
          .catch((error) => console.error(error));
      }
    },
    created() {
      this.debouncedEdit = _.debounce(this.edit, 300);
    },
    mounted() {
      const self = this;
      this.$nextTick(function () {
        self.reload();
      })
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  * {
    box-sizing: border-box;
  }

  button {
    display: inline-block;
    background-color: #6fa5ff;
    color: #fff;
    text-transform: uppercase;
    border: none;
    border-radius: 3px;
    padding: 5px;
    margin-bottom: 10px;
  }

  table {
    border-radius: 3px;
    border: 1px solid #6fa5ff;
    border-spacing: 0;
    border-collapse: collapse;
  }

  td {
    border: 1px solid #6fa5ff;
  }

  thead td {
    position: relative;
    cursor: pointer;
    padding: 0 40px 0 0;
  }

  input {
    border: 1px solid transparent;
    width: 100%;
    height: 100%;
    padding: 3px;
    display: block;
    outline: none;
  }

  input:focus {
    outline: none;
  }

  input:focus {
    border: 1px solid #cecece;
  }

  .sortBtn {
    content: '';
    position: absolute;
    width: 40px;
    height: 10px;
    right: 0;
    top: 50%;
    transform: translateY(-50%);
  }

  .sortBtn::before {
    content: '';
    width: 0;
    height: 0;
    transform: translateY(-50%);
    border-style: solid;
    position: absolute;
    right: 10px;
    top: 50%;
    opacity: 0.3;
    transition: opacity .3s;
    border-width: 6px 6px 0 6px;
    border-color: #007bff transparent transparent transparent;
  }

  .sortBtn:hover::before {
    opacity: 0.5;
  }

  .active .sortBtn::before {
    opacity: 1;
  }

  .desc .sortBtn::before {
    border-width: 6px 6px 0 6px;
    border-color: #007bff transparent transparent transparent;
  }

  .asc .sortBtn::before {
    border-width: 0 6px 6px 6px;
    border-color: transparent transparent #007bff transparent;
  }
</style>
