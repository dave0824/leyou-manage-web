<template>
  <v-card>
      <v-flex xs12 sm10>
        <v-tree url="/item/category/list"
                :isEdit="isEdit"
                @handleAdd="handleAdd"
                @handleEdit="handleEdit"
                @handleDelete="handleDelete"
                @handleClick="handleClick"
                ref="tree"
        />
      </v-flex>
  </v-card>
</template>

<script>
  export default {
    name: "category",
    data() {
      return {
        isEdit:true
      }
    },
    methods: {
      handleAdd(node) {
        console.log("add .... ");
        console.log(node);
        this.$http.post('/item/category/add?'+  this.$qs.stringify(node)).then(res => {
            console.log(res.data)
          this.$refs.tree.getData() // 为了刷新新建类别的id，目前因为其它方法会很大程度改变TreeItem，所以暂时先将就着
        })
      },
      handleEdit(id, name) {
        console.log("edit... id: " + id + ", name: " + name)
        this.$http.put("/item/category/" + id + "?name=" + name)
      },
      handleDelete(id) {
        console.log("delete ... " + id)
        this.$http.delete("/item/category/"+id)
      },
      handleClick(node) {
        console.log(node)
      }
    }
  };
</script>

<style scoped>

</style>
