<template>
  <div class="crypto">
    <div class="crypto-list">
      <p class="title">Cryptocurrencies</p>
      <div class="item" v-for="item in cryptoList" :key="item.id">
        <p class="name">{{item.name}}</p>
        <p class="price">{{item.price | priceFilter}}</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'CryptoList',
  data: function(){
    return {
      cryptoList: [],
      url: 'https://min-api.cryptocompare.com/data/top/mktcapfull?',
      apiKey: "4a58a0473f62191f4300ed62f0c62a0ca0cd345806a963c77b064e83096122d6",
      page: 0,
      limit: 100,
      updateTime: 10000,
      intervalId: 0
    }
  },
  methods: {
    getData (){
      axios
      .get(this.url + 'limit=' + this.limit + '&tsym=USD' + '&page=' + this.page + '&api_key=' + this.apiKey)
      .then(response => {
        let arData = [];
        response.data.Data.forEach((element) => {
          if(!element.RAW) return;
          arData.push({
            id: element.CoinInfo.Id,
            name: element.CoinInfo.FullName,
            shortName: element.CoinInfo.Name,
            price: element.RAW.USD.PRICE
          });
        });
        this.cryptoList = [...this.cryptoList, ...this.sortNewData(arData)];
      });
    },
    updateData (){
        let currentCrypto = {};
        let arrRequestStrings = ["1"];
        this.cryptoList.forEach((element, index) => {
          currentCrypto[element.shortName] = index;
          if((arrRequestStrings[arrRequestStrings.length - 1]).length + element.shortName.length + 1 < 300){
            arrRequestStrings[arrRequestStrings.length - 1] += element.shortName + ',';
          }else{
            arrRequestStrings.push(element.shortName + ',');
          }
        });

        arrRequestStrings.forEach((requestString) => {
          axios
            .get('https://min-api.cryptocompare.com/data/pricemulti?fsyms=' + requestString + '&tsyms=USD' + '&api_key=' + this.apiKey)
            .then(response => {
              Object.entries(response.data).forEach(([key, element]) => {
                this.cryptoList[currentCrypto[key]].price = element.USD;
              });
            });
        });
    },
    sortNewData (arr){
      return arr.sort((a, b) => {
        return b.price - a.price;
      });
    },
    endlessScroll () {
      window.onscroll = () => {
        if (document.documentElement.scrollTop + window.innerHeight === document.documentElement.offsetHeight) {
          this.page++;
          this.getData();
        }
      };
    },
    autoUpdate (){
      if(this.intervalId !== 0) return;
      this.intervalId = setInterval(() => {
        this.updateData();
      }, this.updateTime);
    }
  },
  mounted() {
    this.getData();
    this.endlessScroll();
    this.autoUpdate();
  },
  filters: {
    priceFilter (value){
      return value.toFixed(2) + ' $';
    }
  }
}
</script>

<style scoped>
p{
  margin-bottom: 0;
}
.crypto-list{
  margin: 50px auto;
  width: 400px;
}
.item{
  width: 100%;
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  flex-wrap: wrap;
}
.crypto-list .name{
  font-weight: bold;
}
.title{
  font-weight: bold;
  font-size: 20px;
}
</style>
