<template>
  <div class="at-pagination">{{t}}</div>
</template>
<script>
  export default {
    name:'AtPagination',

    props:{
      t:String
    },
    data:function(){
      return {

      }
    },
    created() {
    },
    methods:{

    }
  }
</script>
<style type="scss" scoped>
  .at-pagination{
    width: 120px;
    height: 50px;
  }
</style>
