<template>
    <div>
      <v-card>
        <v-card-title>
          <v-btn class="primary" @click = "addBrand">新增品牌</v-btn>
          <v-spacer/>
          <v-flex sm3>
            <v-text-field label="输入关键字搜索" v-model.lazy="search" append-icon="search" hide-details></v-text-field>
          </v-flex>
          <v-flex sm12>
              <v-data-table
                :headers="headers"
                :items="desserts"
                :pagination.sync="pagination"
                :total-items="totalBrands"
                :loading="loading"
                class="elevation-1"
              >
                <template slot="items" slot-scope="props">
                  <td class="text-xs-center">{{ props.item.id }}</td>
                  <td class="text-xs-center">{{ props.item.name }}</td>
                  <td class="text-xs-center">
                    <img v-if="props.item.image" :src="props.item.image" width="130" height="40">
                    <span v-else>无</span>
                  </td>
                  <td class="text-xs-center">{{ props.item.letter }}</td>
                  <td class="justify-center layout px-0">
                    <v-btn icon @click="editBrand(props.item)">
                      <i class="el-icon-edit"/>
                    </v-btn>
                    <v-btn icon @click="deleteBrand(props.item)">
                      <i class="el-icon-delete"/>
                    </v-btn>
                  </td>
                </template>
              </v-data-table>
          </v-flex>
        </v-card-title>
        <!--弹出的对话框-->
        <v-dialog max-width="500" v-model="show" persistent scrollable>
          <v-card>
            <!--对话框的标题-->
            <v-toolbar dense dark color="primary">
              <v-toolbar-title>{{isEdit ? '修改' : '新增'}}品牌</v-toolbar-title>
              <v-spacer/>
              <!--关闭窗口的按钮-->
              <v-btn icon @click="closeWindow"><v-icon>close</v-icon></v-btn>
            </v-toolbar>
            <!--对话框的内容，表单-->
            <v-card-text class="px-5" style="height:400px">
              <brand-form @close="closeWindow" :oldBrand="oldBrand" :isEdit="isEdit"/>
            </v-card-text>
          </v-card>
        </v-dialog>
      </v-card>
    </div>
</template>

<script>
  import BrandForm from "./BrandForm"
    export default {
        name: "MyBrand",
        components:{
          BrandForm
        },
        data(){
          return {
            search: '', // 搜索信息
            pagination: {}, // 分页信息
            totalBrands: 0, // 总条数
            loading: false, // 是否显示加载过程
            desserts: [], // 品牌数组
            headers: [
              { text: '品牌id',align: 'center', value: 'id',sortable: true},
              { text: '名称',align: 'center', value: 'name' ,sortable:false},
              { text: 'LOGO',align: 'center',value: 'image'  ,sortable:false},
              { text: '首字母',align: 'center', value: 'letter' ,sortable:true},
              { text: '操作',align: 'center',value: 'id',sortable:false},
            ],
            show: false,// 控制对话框的显示
            oldBrand: {}, // 即将被编辑的品牌数据
            isEdit: false, // 是否是编辑
          }
        },
      mounted(){
        this.getDataFromServer();
      },
      watch:{
        pagination: { // 监听pagination的属性变化
          deep: true, // deep为true，会监视pagination的属性及属性中的对象属性变化
          handler(){
            this.getDataFromServer() // 变化后重新搜索
            }
          },
          search: {
            handler(){
              this.getDataFromServer()
            }
          }
      },

      methods:{

        getDataFromServer(){ // 从服务器加载品牌列表数据和分页数据
          this.loading = true; // 将加载状态设置为true
          this.$http.get("/item/brand/page",{
            params:{
              key: this.search, // 搜索条件
              page: this.pagination.page, // 当前页
              rows: this.pagination.rowsPerPage,// 每页大小
              sortBy: this.pagination.sortBy,// 排序字段
              desc: this.pagination.descending// 是否降序
            }
          }).then(res => {
            this.desserts = res.data.items;
            this.totalBrands = res.data.total;
          })
          this.loading = false; // 数据加载完毕后将加载状态设置为false
        },

        addBrand() { // 新增按钮方法
          // 修改标记
          this.isEdit = false;
          // 控制弹窗可见：
          this.show = true;
          // 把oldBrand变为null
          this.oldBrand = null;
        },

        closeWindow(){ // 关闭窗口
          this.getDataFromServer(); // 重新加载数据
          this.show = false;
          this.oldBrand = null;
        },


      },

    }
</script>

<style scoped>

</style>
