<template>
    <div class="searchPictureHome" :style="{height:mainMenuList.length>0?'auto':'calc(100vh)'}">
        <!-- <Tab :path="path" :tabindex="TabIndex"></Tab> -->
        <!--头部-->
        <div class="header" id="header" :style="{height:searchNum>0?'auto':'calc(100%)'}">
            <img class="headerImg" v-if="searchNum>0" src="../../../assets/image/searchPicturePC/homeBg.png"/>
            <img class="headerImg" v-else style="height: 100%" src="../../../assets/image/searchPicturePC/homeBgNoData.jpg"/>
            <div class="op" :style="{height: searchNum>0?'600px':'100%'}"></div>
            <!-- <img class="logo" src="../../../assets/image/searchPicturePC/logop.png"/> -->
            <!--顶部个人信息-->
            <div class="userInfo">{{userName}}，您好</div>
            <!--花型管理按钮-->
            <div class="headerBtn" @click="goToManage">花型管理</div>
            <!--标题-->
            <div class="headerTitle" v-if="!isStartUpload">以图识图</div>
            <!--搜索-->
            <div class="searchAreaBg" v-if="!isStartUpload">
                <div class="searchArea">
                    <div class="input" ></div>
                    <div class="uploadBtn">本地上传</div>
                    <input type="file"  class="fileInput" ref="file" id="file" placeholder=""  @change="chooseFile" accept="image/*"/>
                </div>
            </div>
            <!--搜索中-->
            <div class="searchLodingBg" v-if="isStartUpload">
                <div class="searchLoding">
                    正在识别中，请稍后
                </div>
                <img class="closeBtn" @click="searchShow" src="../../../assets/image/searchPicturePC/close.png"/>
            </div>
        </div>
        <div class="searchImg" v-if="uploadResult.path!=''&&searchNum>0">
            <img :src="uploadResult.path">
        </div>
        <div class="content" v-if="mainMenuList.length > 0" :style="{marginTop:mainMenuList.length>0?'120px':'0px'}">
            <div class="sameTip">相似图片</div>
            <div class="box">
                <div  class="col" ref="col1" >
                    <div :class="{item:true,itemLast:(index+1)%4==0}" v-for="(item,index) in mainMenuList" :key="item.ProductId"  v-if="(index+1)%4==1" @mouseover="showDetailId=index" @mouseleave="showDetailId=-1">
                        <!-- <img :src="imageRequestUrl + item.customContent" @click="handleView(item)" /> -->
                        <el-image :src="imageRequestUrl + item.customContent" @click="handleView(item)"></el-image>
                        <div class="imgName" :title="item.picName">{{item.picName}}</div>
                        <div class="deleteArea" v-show="index==showDetailId">
                            <div @click="deleteP(item)">试衣展示</div>
                            <div @click="addToCar(item)">选择花型</div>
                        </div>
                    </div>
                </div>
                <div  class="col" ref="col1">
                    <div :class="{item:true,itemLast:(index+1)%4==0}" v-for="(item,index) in mainMenuList" :key="item.ProductId"  v-if="(index+1)%4==2" @mouseover="showDetailId=index" @mouseleave="showDetailId=-1">
                        <!-- <img :src="imageRequestUrl + item.customContent" @click="handleView(item)" /> -->
                        <el-image :src="imageRequestUrl + item.customContent" @click="handleView(item)"></el-image>
                        <div class="imgName" :title="item.picName">{{item.picName}}</div>
                        <div class="deleteArea" v-show="index==showDetailId">
                            <div @click="deleteP(item)">试衣展示</div>
                            <div @click="addToCar(item)">选择花型</div>
                        </div>
                    </div>
                </div>
                <div  class="col" ref="col1">
                    <div :class="{item:true,itemLast:(index+1)%4==0}" v-for="(item,index) in mainMenuList" :key="item.ProductId" v-if="(index+1)%4==3" @mouseover="showDetailId=index" @mouseleave="showDetailId=-1">
                        <!-- <img :src="imageRequestUrl + item.customContent" @click="handleView(item)" /> -->
                        <el-image :src="imageRequestUrl + item.customContent" @click="handleView(item)"></el-image>
                        <div class="imgName" :title="item.picName">{{item.picName}}</div>
                        <div class="deleteArea" v-show="index==showDetailId">
                            <div @click="deleteP(item)">试衣展示</div>
                            <div @click="addToCar(item)">选择花型</div>
                        </div>
                    </div>
                </div>
                <div  class="col" ref="col1" style="margin-right: 0px">
                    <div :class="{item:true,itemLast:(index+1)%4==0}" v-for="(item,index) in mainMenuList" :key="item.ProductId" v-if="(index+1)%4==0" @mouseover="showDetailId=index" @mouseleave="showDetailId=-1">
                        <!-- <img :src="imageRequestUrl + item.customContent" @click="handleView(item)" /> -->
                        <el-image :src="imageRequestUrl + item.customContent" @click="handleView(item)"></el-image>
                        <div class="imgName" :title="item.picName">{{item.picName}}</div>
                        <div class="deleteArea" v-show="index==showDetailId">
                            <div @click="deleteP(item)">试衣展示</div>
                            <div @click="addToCar(item)">选择花型</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        
        <!-- 底部购物车 -->
        <div class="bottomCarArea">
            <div class="title">订单花型列表</div>
            <div class="carDetails">
                <div class="carDetail" v-for="(item,index) in carList" :key="'carDetail'+index">
                    <div class="carImgBg"><img class="carImg" :src="item.url"></div>
                    <img @click="removeCarImg(item)" class="deleteBtn" src="../../../assets/image/order/deleteImg.png">
                </div>
                <div class="carDetail toOrder" @click="goToOrder">
                    <div class="carImgBg toOrderImg"><img class="carImg" src="../../../assets/image/order/addBtn.png"><div class="toOrderTitle">新建订单</div></div>
                </div>
            </div>
        </div>
        <div class="pageBox" v-if="mainMenuList.length > 0">
            <el-pagination
                    background
                    layout="prev, pager, next"
                    :total="total"
                    :page-size="pageSize"
                    @prev-click="prevClick"
                    @next-click="nextPage"
                    @current-change = "currentChange"

            >
            </el-pagination>
        </div>
        <div class="content" v-if="mainMenuList.length <= 0&&searchNum>0" style="margin-bottom: 60px;">暂无数据</div>
        <div style="display: none" ref="images"><img ref="image" :src="imgSrc" :alt="imgName"></div>
        <InfoBox  v-if="showInfo==1 || showInfo==0" :code="showInfo" :errMsg="errMsg" :succMsg="succMsg"></InfoBox>
        <Loading v-if="showLoading"></Loading>
        <a v-if="mainMenuList.length > 0&&searchNum>0" href="#header" class="el-backtop" style="right: 40px; bottom: 40px;"><i class="el-icon-caret-top"></i></a>
    </div>
</template>

<script>
    import InfoBox from '../common/InfoBox';
    import Loading from '../Loading';
    import axios from 'axios';
    import Viewer from 'viewerjs'
    import Cookie from '../../../utils/js/CookieUtil'
    import 'viewerjs/dist/viewer.css'
    import {mapMutations} from 'vuex'
    import {Message} from 'element-ui';
    export default {
        name: "OwnSearchPictureHome",
        components:{
            InfoBox,
            Loading
        },
        data(){
            return {
                requestURL:{
                    // 文件上传
                    file:'/file',
                    // 以图识图
                    pic:'/pic/pic',
                    // 识图结果点击
                    picClick:'/pic/click'
                },
                TabIndex: 98,
                showInfo:-1,
                errMsg:'',
                succMsg:'',
                mainMenuList: [],
                dataList1: [],
                dataList2: [],
                dataList3: [],
                dataList4: [],
                page:1,
                pageSize:8,
                total:0,
                name:'',
                showDetailId:-1,
                headerBg:require('../../../assets/image/searchPicturePC/homeBg.png'),
                showLoading:false,
                userName:'',
                //图片上传结果
                uploadResult:{
                    path:''
                },
                //是否开始上传图片
                isStartUpload:false,
                // 控制滚动加载
                mineFlag: false,
                imgSrc:'',
                file:null,
                imgName:'',
                searchNum:0,
                // 网络图片地址头长度
                baseUrlLength:0,
                // 用户类型，用于控制页面上的按钮显示，1---业务员，只能搜图和查看花型列表，2---厂长，拥有所有权限
                userType: 1,
                file:'',
                imageRequestUrl:'',
                resultImageList:[],
                pageNum:1,
                pageTotal:0,
                carList:[]
            }
        },
        methods:{
            ...mapMutations([
                'saveLoginInfo'
            ]),
            selectCol() {
                let _this = this;
                var getHeight = (ref) => {
                    return _this.$refs[ref].offsetHeight
                }
                var height1 = getHeight('col1')
                var height2 = getHeight('col2')
                var height3 = getHeight('col3')
                var height4 = getHeight('col4')
                switch (Math.min(height1, height2, height3, height4)) {
                    case height1:
                        return 'dataList1'
                        break
                    case height2:
                        return 'dataList2'
                        break
                    case height3:
                        return 'dataList3'
                    case height4:
                        return 'dataList4'
                        break
                }
            },
            goToManage(){
                this.$router.push({
                    path: '/Addpicture'
                });
            },
            chooseFile(){
                this.isStartUpload=true;
                let _this = this;
                if(this.$refs.file.files[0].size/1024/1024 < 20){
                    this.file = this.$refs.file.files[0];
                    this.mainMenuList=[];
                    this.dataList1=[];
                    this.dataList2=[];
                    this.dataList3=[];
                    this.dataList4=[];
                    this.page=1;
                    this.getList(this.$refs.file.files[0]);
                }else{
                    Message({message:'图片大小超过限制',type:"error"})
                };
            },
            getList(file){
                let _this = this;
                let formData = new FormData();
                formData.append('file',file);
                formData.append('page',this.page);
                formData.append('rows',100);
                this.mainMenuList = []
                axios.post(_this.requestURL.pic,formData).then((res)=>{
                    if(res.data.code == 0){
                        let data = res.data.data.searchResult;
                        let url = res.data.wwwFileBaseUrl;
                        _this.imageRequestUrl = res.data.wwwFileBaseUrl
                        _this.isStartUpload = false;
                        _this.mineFlag = true;
                        _this.uploadResult.path = url + res.data.data.searchImgUrl;
                        if(data.auctions.length>0) {
                            Message({message:'搜索成功！',type:"success"})
                             _this.resultImageList = data.auctions;
                            _this.mainMenuList = _this.resultImageList.slice(0,_this.pageSize);
                            _this.total = data.auctions.length;
                            _this.pageTotal = Math.ceil(_this.resultImageList.length / _this.pageSize);
                            _this.baseUrlLength = res.data.wwwFileBaseUrl.length;
                            _this.searchNum = data.auctions.length;
                            _this.$forceUpdate();
                        }else{
                            Message({message:'没有找到结果',type:"error"})
                        }
                        _this.isStartUpload=false;
                    }else if(res.data.code == -1){
                        _this.isStartUpload = true;
                    }
                })
            },
            searchShow(){
                this.isStartUpload = false;
            },
            // 点击上一页
            prevClick(){
                this.pageNum--;
                this.listChange();
            },
            // 点击下一页
            nextPage(){
                this.pageNum++;
                this.listChange();
            },
            // 点击分页数
            currentChange(e){
                this.pageNum = e;
                this.listChange();
            },
            // 分页数据切换
            listChange(){
                let num = this.pageNum - 1;
                let start = num * this.pageSize;
                let end = start + this.pageSize;
                this.mainMenuList = [];
                this.mainMenuList = this.resultImageList.slice(start,end);
                window.scrollTo(0,500);
            },
            setImgPath(result){
                this.isStartUpload=false;
                this.uploadResult = result;
                this.mainMenuList=[];
                this.dataList1=[];
                this.dataList2=[];
                this.dataList3=[];
                this.dataList4=[];
                this.page=1;       
                this.getList();
            },
            setIsStartUpload(result){
                this.isStartUpload = result;
            },
        // 
            // clickCount(item){
            //     let self = this;
            //     let formData = new FormData();
            //     formData.append('search_img_url',self.uploadResult.path);
            //     formData.append('result_img_url',item.CustomContent);
            //     formData.append('product_id',item.ProductId);
            //     let obj = {}
            //     obj.formdata = formData;
            //     obj.url = self.all.baseUrl + '/own/userPicSearch/addClick';
            //     self.getData(obj).then(res => {

            //     });
            // },
        // 
            loadData() {
                // window.onscroll = () => {
                //     let scrollTop = document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop;
                //     let bottomOfWindow = document.body.clientHeight + scrollTop === document.body.scrollHeight;
                //     if (bottomOfWindow && this.mineFlag) {
                //         this.mineFlag = false;
                //         if(this.page+1<=this.total/this.pageSize){
                //             // this.page+=1;
                //             // this.getList(this.file);
                //         }
                //     }
                // }
            },
            handleView (item) {
                this.srcList = [];
                this.imgName=item.PicName
                this.imgSrc =this.imageRequestUrl + item.customContent
                this.$post(this.requestURL.picClick,{
                    searchImgUrl:this.uploadResult.path.substring(this.baseUrlLength),
                    resultImgUrl:item.customContent,
                    productId:item.productId
                }).then((res)=>{})
                const viewer = new Viewer(this.$refs.images, {
                    url: 'data-original',
                    hidden: () => {
                        viewer.destroy()
                        document.body.style.cssText = '';
                    },
                    title:true,
                    navbar: false,
                    toolbar: false
                })
                // 模拟点击事件
                this.$refs.image.click();
            },
            // 进入3D展示页面
            deleteP(item){
                localStorage.removeItem('image');
                localStorage.setItem('image',this.imageRequestUrl + item.customContent);
                this.$router.push({
                    name:'ThreeD'
                })
            },
            //加入购物车
            addToCar(item){
                let nowPic = {
                    name:item.picName,
                    url:this.imageRequestUrl+item.customContent,
                    response:{
                        data:item.customContent
                    }
                }
                if(JSON.stringify(this.carList).indexOf(JSON.stringify(nowPic))<0){
                    this.carList.push(nowPic)
                    localStorage.setItem("carList",JSON.stringify(this.carList))
                }
                
            },
            //移出购物车
            removeCarImg(item){
                let carListResult=[]
                for(var i=0;i<this.carList.length;i++){
                    if(JSON.stringify(this.carList[i])!=(JSON.stringify(item))){
                        carListResult.push(this.carList[i]);
                    }
                }
                this.carList = carListResult;
                localStorage.setItem("carList",JSON.stringify(this.carList))
            },
            //新建订单
            goToOrder(){
                this.$router.push("/message")
            }
        },
        mounted(){
            if(localStorage.getItem("userInfo")){
                let userInfo = JSON.parse(localStorage.getItem("userInfo"));
                this.userName= userInfo.name;
                this.userType = userInfo.role;
                // this.loadData();
                this.searchNum=0;
                document.addEventListener('touchmove', function (event) {
                    window.event.returnValue = true;
                }, false);
            }else{
                let _this = this;
                Message({message:'您还未登录！',type:"error"})
                setTimeout(function(){
                    _this.$router.push({
                        path:"/picLogin"
                    })
                },1000)
            }
            if(localStorage.getItem("carList")){
                this.carList = JSON.parse(localStorage.getItem("carList"))
            }
        },
        computed: {
            isIe() {
                var tmp = window.navigator.userAgent.indexOf("MSIE") >= 1
                    || !!window.ActiveXObject
                    || "ActiveXObject" in window
                return tmp ? "" : "list"
            }

        }
    }
</script>

<style scoped>
    .searchPictureHome{
        min-width: 1200px;
        width: 100vw;
        background-color: #fff;
    }
    .header{
        width: 100%;
        height: 600px;
        position: relative;
        text-align: center;
        overflow: hidden;
    }
    .header img{
        width: 1920px;
        height: 600px;
    }
    .userInfo{
        position: absolute;
        top: 21px;
        right: 113px;
        display: flex;
        align-items: right;
        font-family: PingFangSC-Medium;
        font-size: 20px;
        color: #FFFFFF;
    }
    .headerBtn{
        width: 89px;
        height: 29px;
        border: 1px solid #FFFFFF;
        font-family: PingFangSC-Medium;
        font-size: 16px;
        color: #FFFFFF;
        text-align: center;
        position: absolute;
        top: 58px;
        right: 113px;
        border-radius: 4px;
        text-align: center;
        line-height: 29px;
        cursor: pointer;
    }

    .headerTitle{
        position: absolute;
        top: 17rem;
        width: 100%;
        font-family: PingFangSC-Medium;
        font-size: 40px;
        color: #FFFFFF;
    }
    .searchAreaBg{
        width: 100%;
        height: 83px;
        position: absolute;
        top: 23.38rem;
        text-align: center;
    }
    .searchArea{
        width: 978px;
        height: 81px;
        margin: 0 auto;
        background: #FFFFFF;
        border: 1px solid #FFFFFF;
        display: flex;
        font-family: PingFangSC-Medium;
        font-size: 18px;
        cursor: pointer;
    }
    .input{
        width: 759px;
        height: 100%;
    }
    .leftInput{
        width: 100%;
        height: 100%;
        border: none;
        outline:none;
        text-indent: 2em;
        font-size: 18px;
    }
    .fileInput{
        position: absolute;
        width: 979px;
        height: 83px;
        top: 0;
        left: calc(50% - 490px);
        opacity: 0;
    }
    .uploadBtn{
        color: #FFFFFF;
        width: 221px;
        height: 83px;
        background: #3A8BFB;
        margin-top: -1px;
        margin-right: -1px;
        line-height: 83px;
        text-align: center;
        cursor: pointer;
    }
    .content{
        width: 100%;
        margin-top: 120px;
        /*float: left;*/
        min-width: 1200px;
    }
    .box{
        margin: 0 auto;
        width: 1184px;
        margin-bottom: 40px;
    }
    .col{
        float: left;
        width: 275px;
        margin-right: 28px;
    }
    img{
        width: 100%;
        height: auto;
    }
    .item{
        margin-bottom: 42px;
        width: 275px;
        position: relative;
    }
    .itemLast{
        margin-right: 0px!important;
    }
    .searchLodingBg{
        width: 100%;
        height: 163px;
        position: absolute;
        top: 17rem;
        text-align: center;
    }
    .searchLoding{
        width: 978px;
        height: 163px;
        line-height: 163px;
        margin: 0 auto;
        background: rgba(32,31,31,0.41);
        border: 1px solid #FFFFFF;
        text-align: center;
        font-family: PingFangSC-Medium;
        font-size: 16px;
        color: #fff;
    }
    .closeBtn{
        width: 18px !important;
        height: 18px !important;
        position: absolute;
        top: 23px;
        left: calc(50% + 444px);
    }
    .searchImg{
        width: 178px !important;
        height: 178px !important;
        position: absolute;
        left: 103px;
        top:487px;
    }
    .searchImg img{
        width: 100%;
        height: 100%;
    }
    .sameTip{
        margin-top: 47px;
        text-align: left;
        font-family: PingFangSC-Medium;
        font-size: 20px;
        color: #333333;
        text-indent: 102px;
        margin-bottom: 80px;
    }
    .op{
        width: 100%;
        height: 600px;
        opacity: 0.38;
        background: #000000;
        position: absolute;
        top: 0px;
    }
    .logo{
        left: 103px;
        top: 32px;
        position: absolute;
        width: 85px !important;
        height: auto !important;
        cursor:unset;
    }
    .el-backtop{
        position: fixed;
        background-color: #fff;
        width: 40px;
        height: 40px;
        border-radius: 50%;
        color: #409eff;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 20px;
        box-shadow: 0 0 6px rgba(0,0,0,.12);
        cursor: pointer;
        z-index: 5;
    }
    .imgName{
        width: 100%;
        overflow: hidden;/*超出部分隐藏*/
        text-overflow:ellipsis;/* 超出部分显示省略号 */
        white-space: nowrap;/*规定段落中的文本不进行换行 */
    }
    .pageBox{
        width: 100%;
        margin-top: 42px;
        float: left;
        margin-bottom: 240px;
        padding-left: calc(30%);
    }
    .deleteArea{
        height: 62px;
        line-height: 62px;
        width: 100%;
        color: #fff;
        opacity: 0.65;
        text-align: center;
        background: #000000;
        font-family: PingFangSC-Medium;
        font-size: 14px;
        position: absolute;
        bottom: 23px;
        transition: all 0.5s;
        display: flex;
        justify-content: center;
    }
    .deleteArea div{
        flex: 1;
        cursor: pointer;
    }
    .bottomCarArea{
        height: 240px;
        width: 100%;
        background: #1A2234;
        border: 1px solid #212A3E;
        position: fixed;
        bottom: 0px;
        overflow-x: scroll;
    }
    .bottomCarArea .title{
        font-family: PingFangSC-Semibold;
        font-size: 18px;
        color: #FFFFFF;
        text-align: left;
        margin-left: 120px;
        margin-top: 15px;
        margin-bottom: 30px;
    }
    .bottomCarArea .carDetails{
        display: flex;
        margin-left: 120px;
    }
    .carDetail{
        position: relative;
        margin-right: 52px;
    }
    .carImgBg{
        width: 88px;
        height: 88px;
        background: #FFFFFF;
        border: 1px solid rgba(0,0,0,0.15);
        border-radius: 4px;
        padding: 8px;
    }
    .carImg{
        width: 100%;
        height: 100%;
        border-radius: 4px;
    }
    .deleteBtn{
        width: 21px;
        height: 21px;
        position: absolute;
        top: -10px;
        right: -10px;
        cursor: pointer;
    }
    .clear{
        clear:both; height:0; overflow:hidden;
        margin-bottom: 240px;
    }
    .toOrderImg{
        background-color: #0091FF;
        cursor: pointer;
    }
    .toOrderImg img{
        width: 24px;
        height: 24px;
        margin: 16px 32px;
    }
    .toOrderTitle{
        text-align: center;
        color: #fff;
    }
</style>