<template>
  <div>
    <!-- 上传文件的表单，但是需要隐藏起来，利用其它按钮触发他 -->
 
    
    <!-- 用来触发上传文件框 -->
    <div class="r11"></div>
    <div class="r15"></div>
    <div class="g4t11" v-if="cmode">使用IPFS上传、分享你的画作。 一次上传，永久留存，版权稳妥，无需本地存储，便于分享。</div>

    <input class="g4t12" v-if="!cmode" ></input>
    <div class="g4t21" v-if="cmode">上传你的画作！ 与更多的同好相遇</div>
    <div class="g4t22" v-if="!cmode">与画手相遇！ 鼓励每一个用心创作的人！</div>
    <button class="g5r18"></button>
    <div class="g5t11" v-if="cmode" @click="openFile">即刻上传图片</div>
    <div class="g5t12" v-if="!cmode" @click="pmode=true">查看画手画作</div>>
    <div class="m3" >
      <!--img
        >
      </img-->
      
    <form action="" enctype="multipart/form-data">
      <input
      type="file"
      v-show="false"
      id="fileBox"
      :accept="fileType == 'image' ? 'image/*' : ''"
      @change="getFile"
      :multiple="isMultiple"
      />
      <!--input id="m3file" v-if="cmode" style="position: absolute; top: 0; bottom: 0; left: 0;right: 0; opacity: 0;" type="file" accept="image/gif, image/jpg, image/png" @change="img($event)"/-->
      <div style="text-align: top">
          <span style="font-size: 12px;"></span>
          <img id="m3upload" v-if="cmode" src="../../assets/upload.jpg" style="width: 350px; height: 350px; vertical-align: middle;" />
          <img id="download" v-if="!cmode" src="../../assets/download.png" style="width: 350px; height: 350px; vertical-align: middle;" />
      </div>
    </form>
    </div>
    <div class="r19"></div>

    <div class="t11" v-if="cmode">创作者模式</div>
    <div class="t12" v-if="!cmode">游览者模式</div>
    <button class="g54r17" @click="cmode = !cmode; pmode = false"></button>
    <div class="g54t1">模式切换</div>

    <!-- class="btn" @click="openFile">选择文件</button-->
    <!--span class="number">
      当前选中{{fileData.length}}个文件
    </span-->
    <!-- 循环遍历出所有文件 -->
    <div class="showFile">
      <div
        class="item"
        v-for="(item, index) in fileData"
        :key="index"
        :title="item.name"
      >
        <!-- 放图片的盒子 -->
        <div class="imageBox" v-if="pmode">
          <img
            alt=""
            v-if="
              item.type == 'xls' ||
              item.type == 'xlsx' ||
              item.type == 'doc' ||
              item.type == 'docx' ||
              item.type == 'ppt' ||
              item.type == 'pdf' ||
              item.type == 'txt' ||
              item.type == 'zip' ||
              item.type == '7z' ||
              item.type == 'rar' ||
              item.type == 'jpg' ||
              item.type == 'png' ||
              item.type == 'psd' ||
              item.type == 'ai' ||
              item.type == 'gif' ||
              item.type == 'bmp' ||
              item.type == 'svg' ||
              item.type == 'flv' ||
              item.type == 'mp4' ||
              item.type == 'fla' ||
              item.type == 'mov' ||
              item.type == 'mkv' ||
              item.type == 'wmv' ||
              item.type == 'wav'
            "
          />
          <img src="../../assets/fjIcons/tongyong.png" alt="" v-else />
        </div>
        <!-- 文字 -->

        <!-- 删除按钮 -->
        <div class="icon" @click="deletData(index)">
          <el-button icon="el-icon-delete" circle></el-button>
        </div>
      </div>

      <!-- 下面四个item用于解决flex布局的位置错乱的问题 -->
      <div class="item clear"></div>
      <div class="item clear"></div>
      <div class="item clear"></div>
      <div class="item clear"></div>
      <div class="item clear"></div>
      <div class="item clear"></div>
      <div class="item clear"></div>
    </div>
  </div>
</template>
<script>
const fs = require('fs');
const ipfsFile = require('../../js/ipfsFile');
export default {
  props: {
    cmode: {
      type: Boolean,
      default: true,
    },
    pmode: {
      type: Boolean,
      default: false,
    },
    fileType: {
      type: String,
      default: "image",
    },
    isMultiple: {
      type: Boolean,
      default: true,
    },
    isClear: {
      type: Boolean,
      default: false,
    },
  },
  watch:{
    //所选中的文件发生变化时传递到父组件
    fileData:function(val){
      // console.log(val);
      this.$emit("getFileData",val)
    }
  },
  data() {
    return {
      fileData: [], //每次选择文件后会更新该数据
    };
  },
  methods: {
    //触发选择文件筐
    openFile() {
      //获取文件框file
      var fileBox = document.querySelector("#fileBox");
      this.pmode = true;
      //触发该事件
      fileBox.click();

    },
    img(e) {
      let that = this;//改变this指向
      let files = e.target.files[0];//图片文件名
      if (!e || !window.FileReader) return; // 看是否支持FileReader
      let reader = new FileReader();
      reader.readAsDataURL(files); // 关键一步，在这里转换的
      reader.onloadend = function () {
        that.src = this.result;//赋值
      }
      let param = new FormData(); //转换为表单进行发送给后端
      param.append("imgFile", files); //第一个参数就是后端要接受的字段，要一样，不一样会发送失败
      this.$axios.post(this.$api.ip,param).then((data)=>{
      })
    },
    changepic() {
      var reads= new FileReader();
      f=document.getElementById('m3file').files[0];
      reads.readAsDataURL(f);
      reads.onload=function (e) {
        document.getElementById('m3upload').src=this.result;
      };
    },
    ipfsAdd(addPath) {
      return fs.readFileSync(addPath);
    },
    ipfsGet(hash) {
      return ipfsFile.get(hash);
    },
    //获取选中的内容
    getFile() {
      //获取文件对象
      var fileObj = document.querySelector("#fileBox").files;
      if (this.isClear) {
        //清空用于接收文件的数组
        this.fileData.length = 0;
      }
      //如果设置选项为image，判断一下多选的文件中是否包含非图片的文件
      //下面这段代码是对上传的文件进行过滤判断，如文件大小等其他过滤内容请自行设置，文件名称为item.name,文件大小为item.size,文件类型为item.type
      fileObj.forEach((item) => {
        console.log(item);
        if (this.fileType == "image" && item.type.indexOf("image/") == -1) {
          this.$message({
            type: "warning",
            message: item.name + "为非图片文件，请重新上传",
          });
        } else {
          //将循环出来的文件用数组接收，利于之后的数据处理
          this.fileData.push({
            name: item.name,
            type: item.type,
            size: item.size,
          });
          console.log(this.fileData);
        }
      });
      //循环fileData，获取文件的后缀
      this.fileData.forEach((item) => {
        console.log(item.name.slice(item.name.lastIndexOf(".") + 1));
        item.type = item.name.slice(item.name.lastIndexOf(".") + 1);
        console.log(item);
        // console.log(item.type)
      });
    },
    //删除该文件
    deletData(index) {
      this.$confirm("此操作将删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.fileData.splice(index, 1);
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
    
  },
};
</script>

<style lang="less" scoped>
//按钮样式
.r19 {
  position: absolute;
  width: 108px;
  height: 46px;
  left: 86px;
  top: 57px;
  background: #4285F4;
}
.m3, .fileBox { 
  position: absolute;
  width: 471px;
  height: 628px;
  left: 746px;
  top: 221px;
  filter: drop-shadow(10px 10px 10px rgba(0, 0, 0, 0.25));
  border-radius: 27px;
  // background:url("../../assets/Group60.png");
}
.g4{
  position: absolute;
  width: 620.13px;
  height: 197.69px;
  left: 87px;
  top: 299.31px;
}
.g4t11{
  /* 使用IPFS上传、分享你的画作。 一次上传，永久留存，版权稳妥，无需本地存储，便于分享。 */
  position: absolute;
  width: 546px;
  height: 47px;
  left: 87px;
  top: 450px;

  font-family: Alibaba PuHuiTi;
  font-style: normal;
  font-weight: normal;
  font-size: 18px;
  line-height: 138.5%;
  /* or 25px */

  letter-spacing: 0.08em;
  font-feature-settings: 'pnum' on, 'lnum' on;

  color: #616161;
}
.g4t12{
  position: absolute;
  width: 580px;
  height: 104px;
  left: 86px;
  top: 440px;

  background: rgba(196, 196, 196, 0.1);
  border-radius: 20px;
}
.g4t21, .g4t22{
  /* 上传你的画作！ 与更多的同好相遇 */
  position: absolute;
  width: 619.83px;
  height: 153.99px;
  left: 87.3px;
  top: 299.31px;

  font-family: Alibaba PuHuiTi;
  font-style: normal;
  font-weight: bold;
  font-size: 52px;
  line-height: 126%;
  /* or 66px */

  font-feature-settings: 'pnum' on, 'lnum' on;

  color: #242238;
}

.g5{
  position: absolute;
  width: 304px;
  height: 53.03px;
  left: 86px;
  top: 565px;
}
.g5t11, .g5t12{
/* 即刻上传图片 */
  position: absolute;
  width: 218.2px;
  height: 43px;
  left: 129.1px;
  top: 570px;

  font-family: Alibaba PuHuiTi;
  font-style: normal;
  font-weight: bold;
  font-size: 30px;
  line-height: 41px;
  text-align: center;
  letter-spacing: 0.14em;

  color: #FCFDFE;
}
.g5r18{
  position: absolute;
  width: 304px;
  height: 53.03px;
  left: 86px;
  top: 565px;

  background: #4285F4;
  border-radius: 20px;
}
.t11, .t12{
  position: absolute;
  width: 146px;
  height: 15px;
  left: 211px;
  top: 81px;

  font-family: Raleway;
  font-style: normal;
  font-weight: 800;
  font-size: 20px;
  line-height: 23px;
  letter-spacing: 0.14em;

  color: #4285F4;
}
.g54{
  position: absolute;
  width: 199.08px;
  height: 49.89px;
  left: 1184px;
  top: 46.14px;
}
.g54t1{
  /* 模式切换 */
  position: absolute;
  width: 107.2px;
  height: 14.97px;
  left: 1231.47px;
  top: 61.11px;

  font-family: Raleway;
  font-style: normal;
  font-weight: 800;
  font-size: 20px;
  line-height: 23px;
  letter-spacing: 0.14em;

  color: #4285F4;
}
.g54r17{
  position: absolute;
  width: 199.08px;
  height: 49.89px;
  left: 1184px;
  top: 46.14px;

  background: #FFFFFF;
  border-radius: 5px;
}
.r15{
  position: absolute;
  width: 563px;
  height: 899px;
  left: 877px;
  top: 1px;

  background: #4285F4;
  border-radius: 30px 0px 0px 30px;
}
.r11{
  position: absolute;
  width: 1437px;
  height: 900px;
  left: 0px;
  top: 0px;

  background: #FAFBFD;
}

.btn {
  background: none;
  border: none;
  outline: none;
  padding: 10px 30px;
  border-top: 1px solid #ddd;
  border-bottom: 1px solid #ddd;
  border-image: linear-gradient(rgb(80, 252, 252), rgb(143, 240, 247)) 30 30;
  cursor: pointer;
  // transition: all 1s linear;
  background: none;
  color: rgb(77, 03, 216);
  font-weight: bold;
  background: linear-gradient(rgba(77, 03, 216, 0), rgba(165, 117, 237, 0));
  box-sizing: border-box;
  &:hover {
    background: linear-gradient(rgb(80, 252, 252), rgb(143, 240, 247));
    transition: all 0.5s linear;
    color: #fff;
    border: 1px solid #fff;
    box-sizing: border-box;
  }
}
.number{
  font-size: 14px;
  margin-left: 14px;
}
.showFile {
  position: absolute;
  width: 471px;
  height: 628px;
  left: 746px;
  top: 171px;
  width: 100%;
  padding-right: 20px;
  margin-top: 20px;
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;

  .item {
    width: 12%;
    height: 150px;
    margin-top: 20px;
    transition: all 0.2s linear;
    cursor: pointer;
    position: relative;
    .imageBox {
      width: 100%;
      text-align: center;
      height: 500px;
      margin-top: 10px;
      img {
        height: 500px;
      }
    }
    .text {
      text-align: center;
      margin-top: 5px;
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
    }
    .icon {
      width: 40px;
      height: 40px;
      position: absolute;
      right: 0;
      bottom: 0;
      left: 0;
      top: 0;
      margin: auto;
      display: none;
    }
    &:hover {
      background: rgba(0, 0, 0, 0.1);
      transition: all 0.2s linear;
    }
    &:hover .icon {
      display: block;
    }
  }

  .item.clear {
    border: none;
    height: 0;
    overflow: hidden;
    visibility: hidden;
  }
}
</style>