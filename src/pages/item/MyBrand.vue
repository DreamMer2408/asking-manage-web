<template xmlns:v-slot="http://www.w3.org/1999/XSL/Transform">
  <v-card>
    <v-card-title flat color="white">
      <v-btn color="primary">新增</v-btn>
      <v-spacer/>
      <v-text-field label="输入关键字搜索" v-model="search" append-icon="search" hid-details/>
    </v-card-title>
    <v-data-table
      :headers="headers"
      :items="brands"
      :pagination.sync="pagination"
      :total-items="totalBrands"
      :loading="loading"
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.name }}</td>
        <td class="text-xs-center"><img v-if="props.item.image" :src="props.item.image" width="130" height="40"/></td>
        <td class="text-xs-center">{{ props.item.letter }}</td>
        <td class="text-xs-center">
          <v-icon small class="mr-2" @click="editItem(props.item)">edit</v-icon>
          <v-icon small class="mr-2" @click="deleteItem(props.item)">delete</v-icon>
        </td>
      </template>
    </v-data-table>
  </v-card>
</template>

<script>
  export default {
    name: "myBrand",
    data() {
      return {
        totalBrands: 0,  //总条数
        search: "",    //查询关键字
        brands: [],    //当前页总数据
        loading: true,  //是否在加载中
        pagination: {}, //分页信息
        headers: [
          {text: 'id', align: 'center', value: 'id'},
          {text: '名称', align: 'center', value: 'name', sortable: false},
          {text: 'LOGO', align: 'center', value: 'image', sortable: false},
          {text: '首字母', align: 'center', value: 'letter'},
          {text: '操作', align: 'center', value: 'id', sortable: false}
        ]
      }
    },
    watch: {
      pagination: {
        deep: true,  //深度监视
        handler() {
          this.getDataFromServer();
        }
      },

    search() {
      this.pagination.page = 1;
      this.getDataFromServer();
    }
  },
    methods: {
      getDataFromServer() {
        this.loading = true,
          this.$http.get("/item/brand/page", {
            params: {
              page: this.pagination.page,  //当前页
              rows: this.pagination.rowsPerPage,  //每页条数
              sortBy: this.pagination.sortBy,   //排序字段
              desc: this.pagination.descending,  //是否降序
              key: this.search  //查询字段
            }
          }).then(resp => {
            this.totalBrands = resp.data.total; //总条数
            this.brands = resp.data.items; //品牌数据
            this.loading = false;   //加载完成
          });
      },
    },

    mounted(){
      this.getDataFromServer()
    }
  }
</script>

<style scoped></style>
