<template>
  <div class="pos">
  <el-row>
    <el-col :span='7' class='pos-order' id='order-list'>
        <el-tabs >
            <el-tab-pane label="点餐">
                <el-table :data='tableData' border style='width=100%' max-height="400">
                    <el-table-column prop='goodsName' label='商品名称' ></el-table-column>
                    <el-table-column prop='price' label='商品价格' >
                    </el-table-column>
                    <el-table-column prop='count' label='商品数量'>
                    </el-table-column>
                    <el-table-column  label="操作">
                      <template slot-scope="scope">
                        <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                        <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                      </template>
                   </el-table-column>
                </el-table>
                <div class='total-box'>
                    总价：￥{{totalMoney}}元
                </div>
                <div style='margin-top:10px; clear:both'>
                    <el-button type="danger" @click="delAllGoods()">删除</el-button>
                    <el-button type="success" @click="payMonney()">结账</el-button>
                </div>
            </el-tab-pane>
            <el-tab-pane label="外卖">
            </el-tab-pane>
        </el-tabs>
    </el-col>
    <el-col :span='17'>
        <div>
            <div class='title'>常用商品</div>
            <div class='hot-goods-list'>
                <ul>
                    <li v-for='goods in hotGoodsData' :key="goods.goodId" @click='addOrderList(goods)'>
                        <span>{{goods.goodsName}}</span>
                        <span class='hot-goods-price'>￥{{goods.price}}元</span>
                    </li>
                </ul>
            </div>
            <div class='goods-type'>
                <el-tabs>
                    <el-tab-pane label='汉堡'>
                            <ul class='type-goods-list'>
                                <li v-for='goods in hamburgerData' :key="goods.goodId" @click='addOrderList(goods)'>
                                    <span class='good-img'><img :src="goods.goodsImg" width="100%"></span>
                                    <span class='good-name'>{{goods.goodsName}}</span>
                                    <span class='good-price'>￥{{goods.price}}元</span>
                                </li>
                            </ul>
                    </el-tab-pane>
                    <el-tab-pane label='小食'>
                            <ul class='type-goods-list'>
                                <li v-for='goods in snackData' :key="goods.goodId" @click='addOrderList(goods)'>
                                    <span class='good-img'><img :src="goods.goodsImg" width="100%"></span>
                                    <span class='good-name'>{{goods.goodsName}}</span>
                                    <span class='good-price'>￥{{goods.price}}元</span>
                                </li>
                            </ul>
                    </el-tab-pane>
                    <el-tab-pane label='饮料'>
                            <ul class='type-goods-list'>
                                <li v-for='goods in drinksData' :key="goods.goodId" @click='addOrderList(goods)'>
                                    <span class='good-img'><img :src="goods.goodsImg" width="100%"></span>
                                    <span class='good-name'>{{goods.goodsName}}</span>
                                    <span class='good-price'>￥{{goods.price}}元</span>
                                </li>
                            </ul>
                    </el-tab-pane>
                    <el-tab-pane label='套餐'>
                            <ul class='type-goods-list'>
                                <li v-for='goods in setMealData' :key="goods.goodId" @click='addOrderList(goods)'>
                                    <span class='good-img'><img :src="goods.goodsImg" width="100%"></span>
                                    <span class='good-name'>{{goods.goodsName}}</span>
                                    <span class='good-price'>￥{{goods.price}}元</span>
                                </li>
                            </ul>
                    </el-tab-pane>
                </el-tabs>
            </div>
        </div>
    </el-col>
  </el-row>
</div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Pos',
  data () {
    return {
      totalMoney: 0,
      tableData: [],
      hotGoodsData: [],
      hamburgerData: [],
      snackData: [],
      drinksData: [],
      setMealData: []
    }
  },
  created: function () {
    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods')
      .then(result => {
        this.hotGoodsData = result.data
      })
      .catch(err => {
        console.log(err)
      })
    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods')
      .then(result => {
        this.hamburgerData = result.data[0]
        this.snackData = result.data[1]
        this.drinksData = result.data[2]
        this.setMealData = result.data[3]
      })
      .catch(err => {
        console.log(err)
      })
  },
  methods: {
    addOrderList (goods) {
      let existFlag = false
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId === goods.goodsId) {
          existFlag = true
        }
      }
      if (existFlag) {
        let arr = this.tableData.filter(o => o.goodsId === goods.goodsId)
        arr[0].count++
      } else {
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        }
        this.tableData.push(newGoods)
      }
      this.computerTotal()
    },
    computerTotal () {
      this.totalMoney = 0
      if (this.tableData) {
        this.tableData.forEach((e) => {
          this.totalMoney += e.count * e.price
        })
      }
    },
    delAllGoods () {
      this.tableData = []
      this.totalMoney = 0
    },
    delSingleGoods (goods) {
      this.tableData = this.tableData.filter(o => o.goodsId !== goods.goodsId)
      this.computerTotal()
    },
    payMonney () {
      if (this.totalMoney !== 0) {
        this.$message({
          message: '谢谢惠顾,共支付' + this.totalMoney + '元 ！',
          type: 'success'
        })
        this.delAllGoods()
      } else {
        this.$message.error('订单中没有商品！')
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.pos-order{
    background-color: #f9fafc;
    border-right: 1px solid #c0ccda;
}
.title{
    height: 20px;
    border-bottom: 1px solid #d3dce6;
    background-color: #f9fafc;
    padding: 10px;
}
.hot-goods-list ul li{
    list-style: none;
    float: left;
    border:1px solid #f5f9f2;
    padding: 10px;
    margin: 10px;
    background-color: #f9fafc;
    cursor: pointer;
}
.hot-goods-price{
    color: #58b7ff;
}
.goods-type{
    clear:both;
}
.type-goods-list li{
    list-style: none;
    width:25%;
    border:1px solid #E5E9F2;
    height: auot;
    overflow: hidden;
    background-color:#fff;
    padding: 2px;
    float:left;
    margin: 2px;
    cursor: pointer;
}
.type-goods-list span{
    display: block;
    float:left;
}
.type-goods-list .good-img{
    width: 40%
}
.type-goods-list .good-name{
    font-size: 18px;
    padding-left: 10px;
    color:brown;
}
.type-goods-list .good-price{
    font-size: 16px;
    padding-left: 10px;
    padding-top:10px;
}
.total-box{
  float: left;
  padding: 10px;
  margin: 10px 0 10px 0;
}
</style>
