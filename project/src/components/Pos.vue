<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐" >
            <el-table :data="tableData" border>
              <el-table-column prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column prop="count" label="商品数量"></el-table-column>
              <el-table-column prop="price" label="商品价格"></el-table-column>
              <el-table-column  label="操作" fixed="right">
                <template slot-scope="scope">
                  <el-button type="text" size="small" @click="reduce(scope.row)">删除</el-button>
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="totalDiv">
              <small>数量：</small>{{totalCount}} &nbsp;&nbsp;&nbsp;&nbsp; <small>金额：</small>{{totalMoney}}
            </div>
            <div class="div-btn">
              <el-button type="warning" >挂单</el-button>
              <el-button type="danger" @click="delGoods">删除</el-button>
              <el-button type="success" @click="checkOut">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">挂单</el-tab-pane>
          <el-tab-pane label="外卖">外卖</el-tab-pane>
        </el-tabs>
      </el-col>
      <el-col :span="17">
        <div class="ofen-goods">
          <div class="title">常用商品</div>
          <div class="ofen-goods-list">
            <ul>
              <li v-for="goods in ofenGoods" @click="addOrderList(goods)">
                <span>{{goods.goodsName}}</span>
                <span class="o-price">{{goods.price}}</span>
              </li>
            </ul>
          </div>
        </div>
        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <div>
                <ul class='cookList'>
                  <li v-for="goods in type0Goods"  @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小吃">
              <div>
                <ul class='cookList'>
                  <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <div>
                <ul class='cookList'>
                  <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <div>
                <ul class='cookList'>
                  <li v-for="foods in type3Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="foods.goodsImg" width="100%"></span>
                    <span class="foodName">{{foods.goodsName}}</span>
                    <span class="foodPrice">￥{{foods.price}}</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
          </el-tabs>
        </div>
      </el-col>
    </el-row>
  </div>
</template>
<script>
import axios from 'axios'
export default ({
  name: 'pos',
  data () {
    return {
      tableData: [],
      ofenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalMoney: 0,
      totalCount: 0
    }
  },
  methods: {
  // 添加订单列表的方法
    addOrderList (goods) {
      this.totalCount = 0 // 汇总数量清0
      this.totalMoney = 0
      let isHave = false
    // 判断是否这个商品已经存在于订单列表
      for (let i = 0; i < this.tableData.length; i++) {
        console.log(this.tableData[i].goodsId)
        if (this.tableData[i].goodsId === goods.goodsId) {
        // 存在
          isHave = true
        }
      }
    // 根据isHave的值判断订单列表中是否已经有此商品
      if (isHave) {
      // 存在就进行数量添加
        let arr = this.tableData.filter(o => o.goodsId === goods.goodsId)
        arr[0].count++
      // console.log(arr);
      } else {
      // 不存在就推入数组
        let newGoods = { goodsId: goods.goodsId, goodsName: goods.goodsName, price: goods.price, count: 1 }
        this.tableData.push(newGoods)
      }
    // 进行数量和价格的汇总计算
      this.tableData.forEach((element) => {
        this.totalCount += element.count
        this.totalMoney = this.totalMoney + (element.price * element.count)
      })
    },
    // 删除  让数量和总价清0 重新计算
    reduce (goods) {
      this.totalCount = 0 // 汇总数量清0
      this.totalMoney = 0
      this.tableData = this.tableData.filter(o => o.goodsId !== goods.goodsId)
      console.log(this.tableData)
      this.tableData.forEach((element) => {
        this.totalCount += element.count
        this.totalMoney = this.totalMoney + (element.price * element.count)
      })
    },
    delGoods () {
      this.tableData = []
      this.totalCount = 0 // 汇总数量清0
      this.totalMoney = 0
    },
    checkOut () {
      if (this.totalCount !== 0) {
        this.tableData = []
        this.totalCount = 0 // 汇总数量清0
        this.totalMoney = 0
        this.$message({
          message: '结账成功，感谢您，祝您用餐愉快',
          type: 'success'
        })
      } else {
        this.$message.error('没有选择商品，亲')
      }
    }
  },
  mounted: function () {
    let orderHeight = document.body.clientHeight
    document.getElementById('order-list').style.height = orderHeight + 'px'
  },
  created: function () {
    axios.get('http://jspang.com/DemoApi/oftenGoods.php')
      .then(reponse => {
        this.ofenGoods = reponse.data
      })
  .catch(error => {
    console.log(error)
  })
    axios.get('http://jspang.com/DemoApi/typeGoods.php')
      .then(reponse => {
        console.log(reponse)
        this.type0Goods = reponse.data[0]
        this.type1Goods = reponse.data[1]
        this.type2Goods = reponse.data[2]
        this.type3Goods = reponse.data[3]
      })
  .catch(error => {
    console.log(error)
  })
  }
})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

 .pos-order {
   background-color: #f9fcfa;
   border-right: 1px solid #c0ccda;
   padding-left: 10px;
 }
  .div-btn {
    margin-top:10px;
  }
  .title{
    height:20px;
    border-bottom: 1px solid #d3dce6;
    background-color: #f9fafc;
    padding:10px;
    text-align: left;
  }
  .ofen-goods-list ul li{
    list-style: none;
    float:left;
    border:1px solid lightblue;
    padding:10px;
    margin:10px;
    background-color: #fff;
  }
  .o-price{
    color:lightskyblue;
  }
  .goods-type {
    clear:both;
    margin-left:10px;
  }
 .cookList li{
   list-style: none;
   width:23%;
   border:1px solid #E5E9F2;
   height: auto;
   overflow: hidden;
   background-color:#fff;
   padding: 2px;
   float:left;
   margin: 2px;
   cursor: pointer;
 }
 .cookList li span{

   display: block;
   float:left;
 }
 .foodImg{
   width: 40%;
 }
 .foodName{
   font-size: 18px;
   padding-left: 10px;
   color:brown;

 }
 .foodPrice{
   font-size: 16px;
   padding-left: 10px;
   padding-top:10px;
 }
  .totalDiv {
    background-color: #fff;
    padding: 10px;
    border-bottom: 1px solid #d3dce6;
    border-left: 1px solid #d3dce6;
  }
</style>
