<template>
  <div class="item-card">
    <div class="hover-card">
      <div class="img-container">
        <img :src="file_logo" alt="">
      </div>
      <div class="item-headline" :title="fileData.name" @click="showPic">
        <p class="item-name">{{fileData.file_name}}</p><Icon class="item-delet" @click.stop="del_item(fileData.id)" type="md-close" />
      </div>
    </div>
    
    <div class="item-info">
      <!-- <span>{{fileData.file_type}}</span> -->
      <span style="margin-right: 15px;">大小：<span>{{fileSize}}</span></span>
      <span>上传于：<span>{{fileAddTime}}</span></span>
    </div>
    <Input v-model="fileData.file_url" readonly>
        <a href="javascript:void(0);" class="copyButton" v-clipboard="copyLink" slot="append">复制</a>
    </Input>
  </div>
</template>

<script>
import { delet_file } from '@/api/data'
export default {
  props:{
    fileData: {
      type: Object
    }
  },
  methods: {
    showPic() {
      window.open(this.fileData.file_url, '_blank')
    },
    del_item(id) {
      console.log(this.$Modal)
      this.$Modal.confirm({
          title: '提示',
          content: '<p>是否确定删除？</p>',
          className:"vertical-center-modal",
          onOk: () => {
              delet_file(id).then(res=>{
                console.log(res)
                if(res.data.ret == 200){
                  this.$Message.success('文件删除成功')
                  this.$emit('refresh')
                }
              })
          },
          onCancel: () => {
            console.log("cancel")
              return
          }
      }); 
    }
  },
  computed: {
    fileSize() {
      let size = this.fileData.file_size/1024
      if(size > 1024) {
        size = size/1024
        size = Math.floor(size * 100) / 100 +'MB'
      }else{
        size = Math.floor(size * 100) / 100 + 'KB'
      }
      return size
    },
    fileAddTime() {
      let data_time = this.fileData.add_time
      let time = data_time.slice(0,10)
      return time
    },
    copyLink() {
      return {
        value: this.fileData.file_url,
        success: (e) => {
          this.$Message.success('复制成功')
        },
        error: () => {
          this.$Message.error('复制失败')
        }
      }
    },
    file_logo() {

      //todo 扩展其他文件的logo 使用swtich方式 
      if ( /\.(jpe?g|png|gif)$/i.test(this.fileData.file_url) ) {
          return this.fileData.file_url
        }else if( /\.(xlsx?)$/i.test(this.fileData.file_url) ){
          let logo = require('@/assets/icons/file-icon/xls_logo.jpg')
          return logo
        }else if( /\.(docx?)$/i.test(this.fileData.file_url) ){
          let logo = require('@/assets/icons/file-icon/doc_logo.jpg')
          return logo
        }else if( /\.(pptx?)$/i.test(this.fileData.file_url) ){
          let logo = require('@/assets/icons/file-icon/ppt_logo.jpg')
          return logo
        }else if( /\.(html)$/i.test(this.fileData.file_url) ){
          let logo = require('@/assets/icons/file-icon/html_logo.jpg')
          return logo
        }else {
          let logo = require('@/assets/icons/file-icon/others_logo.jpg')
          return logo
        }
    }
  },
}
</script>

<style lang="less">
  .item-card {
    width: 300px;
    font-size: 14px;
    display: flex;
    flex-flow: column;
    justify-content: space-between;
    margin-top: 30px;

    .img-container {
      width: 100%;
      height: 200px;
      margin-bottom: 5px;
      background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQAQMAAAAlPW0iAAAAA3NCSVQICAjb4U/gAAAABlBMVEXMzMz////TjRV2AAAACXBIWXMAAArrAAAK6wGCiw1aAAAAHHRFWHRTb2Z0d2FyZQBBZG9iZSBGaXJld29ya3MgQ1M26LyyjAAAABFJREFUCJlj+M/AgBVhF/0PAH6/D/HkDxOGAAAAAElFTkSuQmCC");
      
      img {
        width: 100%;
        height: 100%;
        object-fit: contain;
      }
    }

    .item-headline {
      display: flex;
      justify-content: space-between;
      cursor: pointer;
      margin: 0 0 5px 5px;

      .item-name {
        font-weight: 600;
        color: #57a3f3;
        overflow: hidden;
        text-overflow:ellipsis;
        white-space:nowrap;
        width: 280px;
      }

      .item-delet {
        display: none;
        padding-right: 5px;
        padding-top: 5px;
        color: #fff;
      }
    }

    .item-info {
      display: flex;
      justify-content: start;
      color: #838383;
      margin: 5px 0;
      font-size: 12px;
    }

    .copyButton {
      color: #515a6e;
    }

    .copyButton:hover {
      color: #57a3f3;
    }
    
  }

  .item-card:hover {
    .hover-card{
    box-shadow: rgb(223, 223, 223) 1px 1px 4px 4px;
    background: rgb(25, 92, 146);
    
    }
    .item-name {
      color: #fff;
    }
    .item-delet {
      display: inline-block;
    }

    .item-delet:hover {
      display: inline-block;
      color: rgb(255, 60, 0);
    }
  }

  .ivu-modal-wrap .ivu-modal {
      margin-top: 200px;
  }
</style>