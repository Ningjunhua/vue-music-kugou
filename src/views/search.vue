<template>
  <div>
    <div class="search-panel">
      <div class="search-input">
        <span class="search-icon"></span>
        <input id="search-input" type="text" v-model="keyword" placeholder="歌手/歌名/拼音" @keydown.enter="search">
      </div>
      <!-- <a href="javascript:;" @click="search" class="search-btn">搜索</a> -->
      <mt-button type="danger" @click="search" size="small" v-show="disabled" class="search-btn">搜索</mt-button>
    </div>

    <div class="search-list" v-show="togglePanel">
      <div class="search-list-title">热门搜索</div>
       <mt-cell v-for="(item,index) in hotList" :title="item.keyword" @click.native=" replaceInput(index)" :key="index"></mt-cell>
      <div class="rank-info-list"></div>
   </div>

    <div class="songs-list" v-show="!togglePanel">
      <div class="search-total">
        共有{{total}}条结果
      </div>
       <mt-cell v-for="(item,index) in songList" :title="item.filename" @click.native="playAudio(index)" :key="index">
      </mt-cell>
      <div class="rank-info-list">
       
      </div>
      
    </div>
  </div>
</template>

<script type="es6">
import Vue from 'vue'
import Jquery from 'jquery'
import { Indicator, Button, Toast} from 'mint-ui'
import { PLAY_AUDIO } from '../mixins'

export default {
  mixins: [PLAY_AUDIO],
  data() {
    return {
      keyword: '',
      hotList: '',
      togglePanel: true,
      disabled:true,
      total: null,
      songList: []
    }
  },
  created() {
    this.getList()
  },
  methods: {
    getList() {
      Indicator.open({
        spinnerType: 'fading-circle',
        spinnerColor: '#a61c00'
      });
      this.$http.get('/aproxy/api/v3/search/hot?format=json&plat=0&count=30').then(({ data }) => {

        Indicator.close();
        this.hotList = data.data.info

      });
    },
    search() {
     
      if(document.getElementById('search-input').value.length==0){
        
        Toast({
  message: '请输入关键字',
  position: 'middle',
  duration:2000
});
 this.togglePanel = true
      }else if(document.getElementById('search-input').value.length>0){
         
        Indicator.open({
        spinnerType: 'fading-circle',
        spinnerColor: '#a61c00'
      })
       
      if (this.keyword)
        this.$http.get(`/aproxy/api/v3/search/song?format=json&keyword=${this.keyword}&page=1&pagesize=30&showtype=1`).then(({ data }) => {
          this.songList = data.data.info
          this.total = data.data.total
          Indicator.close()
         this.togglePanel = false
        })
      }
      
    },
    replaceInput(index) {
      this.keyword = this.hotList[index].keyword

      
      Indicator.open({
        spinnerType: 'fading-circle',
        color: '#a61c00'
      })
      if (this.keyword)
        this.$http.get(`/aproxy/api/v3/search/song?format=json&keyword=${this.keyword}&page=1&pagesize=30&showtype=1`).then(({ data }) => {
          this.songList = data.data.info
          this.total = data.data.total
          Indicator.close()
          this.togglePanel = false
        })

    }

  }
}
</script>



<style>
.search-btn {
  background-color: #a61c00;
}

.search-list-title {
  color: #a61c00;
}
.hot-search-bar{
  width:calc(100% - 16px);
  margin-left: 8px;
 height: auto;
}
.hot-search{
  margin-bottom: 5px;
  margin-right:5px;
  margin-left: 5px;
  background: #a61c00;
  float: left;
  padding: 8px;
  border-radius: 16px;
  color: white;

}
</style>
