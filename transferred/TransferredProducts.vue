<template>
  <div class="holder">
    <table class="table table-bordered" v-if="data !== null">
      <thead>
        <tr>
          <td>Product Title</td>
          <td>Batch Number</td>
          <td>Manufactured Date</td>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in data" :key="index">
          <td v-if="item.type === 'regular'">{{item.title}} ({{item.product_attribute}})</td>
          <td v-else>{{item.title}}</td>
          <td>{{item.batch_number}}</td>
          <td>{{item.manufacturing_date}}</td>
<!--           <td>
            <i class="fas fa-trash text-danger" @click="remove(item.id)"></i>
          </td> -->
        </tr>
      </tbody>
    </table>
    <empty v-if="data === null" :title="'Transferred product is empty!'" :action="'Check back again later'"></empty>
  </div>
</template>
<style>
  .holder{  
    width: 98%;
    float: left;
    margin-right: 2%;
    margin-top: 25px;
    margin-bottom: 50px;
  }
  .results{
    width: 100%;
    float: left;
    margin-top: 25px;
  }
  .item:hover{
    cursor: pointer;
  }

  .products-holder{
    width: 24%;
    float: left;
    height: 300px;
    margin-right: 1%;
    border: solid 1px #ddd;
    margin-bottom: 10px;
    color: #555;
    margin-top: 25px;
  }
  .products-holder:hover{
    cursor: pointer;
    border: solid 1px #ffaa81;
    background: #ffaa81;
    color: #fff;
  }

  .products-image{
    width: 100%;
    float: left;
    position: relative;
    height: 250px;
    text-align: center;
  }

  .products-image img{
    height: 250px;
    float: left;
    width: 100%;
  }
  .products-image .fa-image{
    font-size: 150px;
    line-height: 250px;

  }
  .products-details{
    height: 50px;
    width: 100%;
    float: left;
    border-top: solid 1px #ddd;
  }
  .products-title{
    width: 50%;
    float: left;
    height: 50px;
  }
  .products-title label{
    width: 100%;
    float: left;
    font-size: 12px;
    margin: 0px !important;
    padding-left: 10px;
  }
  .products-price{
    width: 50%;
    float: left;
    height: 50px;
    line-height: 50px;
    font-weight: 600;
    text-align: right;
    padding-right: 5px;
  }

  .products-wishlist{
    height: 50px;
    text-align: center;
    line-height: 50px;
    width: 100%;
    position: absolute;
    top: 50%;
    z-index: 10;
    display: none;
  }

  .products-wishlist:hover, .products-wishlist i:hover, .products-wishlist label:hover{
    cursor: pointer;
  }

  .products-wishlist label{
    line-height: 50px;
    float: left;
    width: 50%;
    text-align: center;
  }
  
  .products-wishlist i{
    font-size: 32px;
    line-height: 50px;
  }

  .products-holder:hover .products-wishlist{
    display: block;
  }

</style>
<script>
import ROUTER from 'src/router'
import AUTH from 'src/services/auth'
import CONFIG from 'src/config.js'
import axios from 'axios'
import COMMON from 'src/common.js'
export default {
  mounted(){
    this.retrieve()
  },
  data(){
    return {
      user: AUTH.user,
      config: CONFIG,
      errorMessage: null,
      data: null,
      common: COMMON,
      transferId: null
    }
  },
  components: {
    'empty': require('components/increment/generic/empty/Empty.vue')
  },
  methods: {
    redirect(parameter){
      ROUTER.push(parameter)
    },
    retrieve(){
      let parameter = {
        condition: [{
          value: this.$route.params.code,
          column: 'code',
          clause: '='
        }]
      }
      if(this.user.type === 'USER'){
        parameter['user'] = true
      }
      $('#loading').css({'display': 'block'})
      this.APIRequest('transfers/retrieve_transferred_items', parameter).then(response => {
        $('#loading').css({'display': 'none'})
        if(response.data.length > 0){
          this.data = response.data
        }else{
          this.data = null
        }
      })
    },
    remove(id){
      let parameter = {
        id: id
      }
      $('#loading').css({'display': 'block'})
      this.APIRequest('transferred_products/delete', parameter).then(response => {
        this.retrieve(this.transferId)
      })
    }
  }
}
</script>
