<!--疫情球体-->
<template>
  <div class="container" v-loading="isLoading" element-loading-background="rgba(255, 255, 255, 0.8)"
    element-loading-text="数据加载中...">
    <div class="isMobile-div" v-if="mobileDiv">
      <!--手机端遮罩-->
    </div>
    <!--顶部标题-->
    <!-- <div class="top-div">
      <div class="name-div">
        <h2>全球疫情可视化</h2>v{{version}}
        <h4>(截止{{ allData.mtime }})</h4>
      </div>
      <div class="btn-div">
        <el-button class="btn" color="#ff656599" @click="isSphere = true" round>
          <el-icon :size="20" style="margin-right: 10px;">
            <List />
          </el-icon>
          全球数据
        </el-button>
        <el-button class="btn" color="#ff656599" @click="isChina = true" round>
          <el-icon :size="20" style="margin-right:10px;">
            <List />
          </el-icon>
          国内数据
        </el-button>
        <el-button class="btn" color="#ff656599" @click="isEchart = true;" round>
          <el-icon :size="20" style="margin-right: 10px;">
            <TrendCharts />
          </el-icon>
          国内分析
        </el-button>
        <el-button class="btn" color="#ff656599" @click="provinceAnalyze" round>
          <el-icon :size="20" style="margin-right: 10px;">
            <TrendCharts />
          </el-icon>
          省内分析
        </el-button>
        <el-button class="btn" color="#3f7495" @click="sureDownloadReport" round>
          <img :src="wordImg">
          下载当地疫情报告
        </el-button>
      </div>
    </div> -->
    <div class="header">
      <div class="header_left">
        <div class="header_left_jiao"></div>
        <div class="header_left_time">{{ nowDateref }} {{ nowTimeref }}</div>
        <div class="header_left_line"></div>
      </div>
      <div class="header_mid">
        <div class="header_mid_top"></div>
        <div class="header_mid-text">疫情可视化系统</div>
        <div class="header_mid_bt"></div>
        <div class="header_mid_lline"></div>
        <div class="header_mid_rline"></div>
        <div></div>
      </div>
      <div class="header_right">
        <div class="header_right_jiao"></div>
        <div class="header_left_weather">
          <span>{{ userMsg.city }}</span>
          {{ weather.wea }} / {{ weather.tem }}℃
        </div>
        <div class="header_right_line"></div>
      </div>
    </div>
    <!--球体盒子-->
    <div id="sphereDiv"></div>

    <!--设置按钮-->
    <div class="set-div">
      <el-icon color="#ffffff88" :size="40" @click="isDrawer = true">
        <Setting />
      </el-icon>
    </div>


    <div class="left_bg">
      <!--全球柱状图-->
      <div class="sphereDataDiv">
        <div class="sphereDataDiv_title">
          累计全球确诊人数前{{ sliceNum }}国家
        </div>
        <!-- <div class="histogramDivDiv">
          <div id="histogramDiv"></div>
        </div> -->
        <div w50rem h24rem p3 flex="~ col" justify-center items-center bg-dark
          style="height: 90%;width: 100%;margin: 10px 0;">
          <dv-scroll-ranking-board :config="top50worldList" style="width:100%;height:100%;" />
        </div>
      </div>
      <!--全过柱状图-->
      <div class="sphereDataDiv1">
        <div class="sphereDataDiv1_title">累计全国确诊人数前3城市</div>
        <div class="histogramDivDiv1">
          <div id="histogramDiv1"></div>
        </div>
      </div>
    </div>


    <!--数字盒子-->
    <div class="right_bg">
      <div class="numDiv">
        <div class="numDiv_title">
          累计全球确诊,治愈，死亡数
        </div>
        <div class="numDiv_border">
          <div class="addconDiv">
            <div class="tit">确诊</div>
            <addNumber class="certain-div" :value="certain" :time="10" :thousandSign="true" />
            <!-- <div class="day-tit">今日{{ othertotal.certain_inc }}</div> -->
          </div>
          <div class="addcureDiv">
            <div class="tit">治愈</div>
            <addNumber class="addcure-div" :value="addcure" :time="10" :thousandSign="true" />
            <!-- <div class="day-tit">今日{{ othertotal.recure_inc }}</div> -->
          </div>
          <div class="addDieDiv">
            <div class="tit">死亡</div>
            <addNumber class="addDie-div" :value="addDie" :time="10" :thousandSign="true" />
            <!-- <div class="day-tit">今日{{ othertotal.die_inc }}</div> -->
          </div>
        </div>
      </div>
    </div>


    <div class="components">
      <!--点的标签-->
      <PointMsg :position="position" :currentPointData="currentPointData" />
      <!--设置抽屉-->
      <SetDrawer :isDrawer="isDrawer" @close="isDrawer = false" @changeSetData="changeSetData" />
      <!--全球数据表格-->
      <!-- <SphereTabDrawer :dvColor="dvColor" :isSphere="isSphere" :sphereData="sphereData" @close="isSphere = false" /> -->
      <!--国内数据表格-->
      <!-- <ChinaTabDrawer :dvColor="dvColor" :allData="allData" :isChina="isChina" :list="allData.list"
        @close="isChina = false" /> -->
      <!--国内图表分析-->
      <!-- <ChinaEchartDrawer :dvColor="dvColor" :sphereData="sphereData" :daily="allData.add_daily"
        :jwsrTop="allData.jwsrTop" :isEchart="isEchart" @close="isEchart = false" :historylist="allData.historylist"
        :allData="allData" /> -->
      <!--省内图表分析-->
      <!-- <ProvinceEchartDrawer :dvColor="dvColor" :isProvinceEchartDrawer="isProvinceEchartDrawer"
        @close="isProvinceEchartDrawer = false" :currentProvinceData="currentProvinceData" /> -->
      <!--报告抽屉-->
      <!-- <ReportDrawer :isReport="isReport" :dvColor="dvColor" :reportData="reportData" @close="isReport = false" /> -->
    </div>
  </div>
</template>
<script lang='ts' setup>
import {
  ref,
  computed,
  watch,
  onMounted,
  getCurrentInstance,
  toRef,
  reactive
} from 'vue';
import * as THREE from "three";
import {
  OrbitControls
} from "three/examples/jsm/controls/OrbitControls";
import * as echarts from "echarts";
import PK from "@/../package.json";
import jsonp from "@/utils/jsonpUtils";
import jsonp1 from "@/utils/jsonpUtils1";
import {
  exportWord
} from "@/utils/downloadDoc.js";
import addNumber from "@/components/addNumber.vue";
import countryPosition from "@/assets/json/countryPosition.json";
import {
  dataSource1,
  dataSource2,
  getWeathers
} from "@/api/request";
import tempData from "@/assets/json/tempWorldData.json";
import createServe from '@/api/http'
import tempProvinceData from "@/assets/json/tempProvinceData.json";
import tempIpData from "@/assets/json/tempIpData.json";
import starImg from "@/assets/img/star.jpg";
import earthImg from "@/assets/img/earth.jpg";
import mapImg from "@/assets/img/map.jpg";
import glow from "@/assets/img/glow.png";
import innerGlow from "@/assets/img/innerGlow.png";
import universeImg from "@/assets/img/universe.jpg";
import earthNightImg from "@/assets/img/earthNight.jpg";
import normalImg from "@/assets/img/earthNormal.jpg";
import virusImg from "@/assets/img/virus3.png";
import PointMsg from "@/components/PointMsg.vue";
import ChinaEchartDrawer from "@/components/ChinaEchartDrawer.vue";
import SetDrawer from "@/components/SetDrawer.vue";
import SphereTabDrawer from "@/components/SphereTabDrawer.vue";
import ChinaTabDrawer from "@/components/ChinaTabDrawer.vue";
import ProvinceEchartDrawer from "@/components/ProvinceEchartDrawer.vue";
import wordImg from "@/assets/img/word.png";
import {
  ElMessage,
  ElMessageBox
} from 'element-plus'
import axios from 'axios';
let version: any = ref(PK.version), //系统版本号
  mobileDiv: any = ref(true), //手机端遮罩
  scene: any = null, //场景(频繁变更的对象放置在vue的data中会导致卡顿)
  camera: any = null, //相机
  dom: any = null, //需要使用canvas的dom
  renderer: any = null, //渲染器
  orbitControls: any = null, //鼠标控件
  mouse = new THREE.Vector2(), //鼠标的二维平面
  raycaster = new THREE.Raycaster(), //光线投射器(用于鼠标点击时获取坐标)
  positionData = countryPosition, //国家位置数据
  isDay = false, //昼夜切换
  isTag = true, //标签显示
  isSphere = ref(false), //全球数据对话框状态
  isChina = ref(false), //国内数据对话框状态
  isEchart = ref(false), //图表分析对话框状态
  anId = ref(0), //动画id
  isLoading = ref(false), //加载状态
  allData: any = ref({}), //疫情所有数据
  othertotal: any = ref({}), //全球基本数据
  sphereData: any = ref([]), //球体数据
  currentPointData: any = ref({}), //当前选中点的数据
  position = ref({
    x: "",
    y: ""
  }), //标签位置
  isDrawer = ref(false), //设置抽屉状态
  histogramChart: any = null, //柱状图
  sliceNum: number = 50, //柱状图截取数量
  certain = ref(0), //全球现存确诊
  addcure = ref(0), //全球治愈数
  addDie = ref(0), //全球死亡数
  userMsg: any = ref({}), //使用者信息
  currentProvinceData: any = ref({}), //当前省数据
  isProvinceEchartDrawer = ref(false), //省内图表对话框
  nowDateref: any = ref(""),
  nowTimeref: any = ref(""),
  top50worldList: any = reactive({
    data: [],
    rowNum: 10,
    unit: '人'
  }),
  weather: any = ref({});
onMounted(() => {
  judgeDevice(); //判断设备
  setInterval(getNowTime, 500);
})

//当allData数据获取完成后开始获取用户ip信息
watch(
  () => allData.value,
  (val) => {
    if (val.times) {
      getLocationMsg(); //获取用户ip信息
    }
  }
)

//判断设备
function judgeDevice() {
  let isMobile = navigator.userAgent.match(
    /(phone|pad|pod|iPhone|iPod|ios|iPad|Android|Mobile|BlackBerry|IEMobile|MQQBrowser|JUC|Fennec|wOSBrowser|BrowserNG|WebOS|Symbian|Windows Phone)/i
  );
  if (isMobile) {
    alert("当前项目暂未适配移动端，请在pc端打开！");
  } else {
    mobileDiv.value = false; //关闭手机端遮罩
    getCOVID19Data(); //获取疫情数据
  }
};
//当前时间
function getNowTime() {
  let yy = new Date().getFullYear();
  let mm = new Date().getMonth() + 1;
  let dd = new Date().getDate();
  let hh = new Date().getHours();
  let ms =
    new Date().getSeconds() < 10 ?
      "0" + new Date().getSeconds() :
      new Date().getSeconds();
  let mf =
    new Date().getMinutes() < 10 ?
      "0" + new Date().getMinutes() :
      new Date().getMinutes();
  // nowTimeref.value = hh + ":" + mf + ":" + ms;
  //ms是秒，这里可以根据自己需要调整格式
  nowTimeref.value = hh + ":" + mf;
  // nowDateref.value = yy + "年" + mm + "月" + dd + "日";
  nowDateref.value = yy + "-" + mm + "-" + dd;
}
//设置切换
function changeSetData(type: string, setData: any) {
  //昼夜切换
  if (type == "isDay") {
    isDay = setData.value.isDay;
    destroyScene(); //销毁
    init(sphereData.value); //重新初始化
  }
  (type == "isDrag") && (orbitControls.enableRotate = setData.value.isDrag); //鼠标旋转
  (type == "isZoom") && (orbitControls.enableZoom = setData.value.isZoom); //鼠标缩放
  (type == "isTag") && (isTag = setData.value.isTag); //标签显示
  (type == "autoRotate") && (orbitControls.autoRotate = setData.value.autoRotate); //自转切换
  (type == "rotateSpeed") && (orbitControls.autoRotateSpeed = setData.value.rotateSpeed / 50) //自转速度
};

//销毁场景
function destroyScene() {
  cancelAnimationFrame(anId.value); //根据动画id停止动画渲染
  renderer.forceContextLoss(); //强制失去上下文
  renderer.dispose();
  scene.clear();
  scene = null;
  camera = null;
  orbitControls = null;
  dom.innerHTML = null;
  renderer = null;
};

//解码返回的unicode
function decodingStr(str: any) {
  let repStr: any = str.replace(/\\/g, "%"); //用%替换\
  let str1 = repStr.split("jsoncallback(")[1]
  let str2 = str1.split(");")[0] //截取出需要的字符串
  let unStr = unescape(str2); //解码出汉字(方法弃用)
  let jsonObj = JSON.parse(unStr); //转换成json对象
  return jsonObj;
};

//获取数据
//开发环境用本地tempData数据
//生产环境用vue代理dataSource1获取api数据
//代理获取失败则使用jsonp获取api数据
function getCOVID19Data() {
  isLoading.value = true;
  if (process.env.NODE_ENV !== "production") {
    dataSource1()
      .then((res) => {
        console.log("vue代理dataSource1获取数据");
        let decodingObj = decodingStr(res.data); //解码unicode
        allData.value = decodingObj.data; //记录所有数据
        structureData(allData.value); //构造数据  
        isLoading.value = false;
      })
      .catch((err) => {
        jsonpGetData();
      });
  } else {
    console.log("使用tempData数据");
    allData.value = tempData.data;
    structureData(allData.value); //构造数据  
    isLoading.value = false;
  }
};

//jsonp方式获取数据
function jsonpGetData() {
  let jsonpUrl: any = process.env.VUE_APP_1; //获取环境变量中的url地址
  let callBackName = "jsoncallback"; //回调名
  jsonp(jsonpUrl, (res: any) => {
    if (res.status.msg = "success") {
      console.log("jsonp获取数据");
      allData.value = res.data; //记录所有数据
    } else {
      console.log("使用tempData数据");
      allData.value = tempData.data;
    }
    structureData(allData.value); //构造数据  
    isLoading.value = false;
  }, callBackName)
};

//构造球体数据
function structureData(COVID19Data: any) {
  let worldlist = JSON.parse(JSON.stringify(COVID19Data.worldlist));
  worldlist.forEach((w: any) => {
    for (let key in positionData) {
      if (w.name == key) {
        w.position = positionData[key];
      }
    }
    //国外数据类型转换
    w.value = Number(w.value); //字符串转换为数字类型
    w.deathNum = Number(w.deathNum);
    w.cureNum = Number(w.cureNum);
  });
  sphereData.value = worldlist;
  othertotal.value = allData.value.othertotal;
  //国内省份数据类型转换
  allData.value.list.forEach((l: any) => {
    l.value = Number(l.value);
    l.econNum = Number(l.econNum);
    l.deathNum = Number(l.deathNum);
    l.cureNum = Number(l.cureNum);
    l.asymptomNum = Number(l.asymptomNum);
    l.jwsrNum = Number(l.jwsrNum);
    //市/区数据类型转换
    if (l.city.length !== 0) {
      l.city.forEach((c: any) => {
        c.conNum = Number(c.conNum);
        c.econNum = Number(c.econNum);
        c.deathNum = Number(c.deathNum);
        c.cureNum = Number(c.cureNum);
        c.asymptomNum = Number(c.asymptomNum);
        c.zerodays = Number(c.zerodays);
      })
    }
  })
  init(sphereData.value); //初始化
  setTimeout(() => {
    initEchart(); //初始化图表
  }, 1000);
};

//初始化球体
function init(data: any) {
  dom = document.getElementById("sphereDiv"); //获取dom
  let width = dom.clientWidth;
  let height = dom.clientHeight;
  scene = new THREE.Scene(); //场景场景
  camera = new THREE.PerspectiveCamera(45, width / height, 1, 1000); //创建透视相机(视场、长宽比、近面、远面)
  camera.position.set(0, 0, 270); //设置相机位置
  camera.lookAt(0, 0, 0);
  //创建渲染器
  renderer = new THREE.WebGLRenderer({
    antialias: true, //抗锯齿
    alpha: true, //透明
  });
  renderer.setClearColor(0x000000, 0.1); //设置场景透明度
  renderer.setSize(width, height); //设置渲染区域尺寸
  dom.appendChild(renderer.domElement); //将渲染器添加到dom中形成canvas
  createUniverse(); //创建宇宙
  createStars(); //创建星辰
  createLight(); //创建光源
  createSphere(data); //创建球体
  createOrbitControls();
  render();
};

//创建宇宙(球形宇宙)
function createUniverse() {
  let universeGeometry = new THREE.SphereGeometry(500, 100, 100);
  let universeMaterial = new THREE.MeshLambertMaterial({
    //高光材质
    map: new THREE.TextureLoader().load(universeImg),
    side: THREE.DoubleSide, //双面显示
  });
  //宇宙网格
  let universeMesh = new THREE.Mesh(universeGeometry, universeMaterial);
  universeMesh.name = "宇宙";
  scene.add(universeMesh);
};

//创建星辰
function createStars() {
  const positions = [];
  const colors = [];
  //星辰几何体
  const starsGeometry = new THREE.BufferGeometry();
  //添加星辰的颜色与位置
  for (let i = 0; i < 1000; i++) {
    let vertex = new THREE.Vector3();
    vertex.x = Math.random() * 2 - 1;
    vertex.y = Math.random() * 2 - 1;
    vertex.z = Math.random() * 2 - 1;
    positions.push(vertex.x, vertex.y, vertex.z);
    let color = new THREE.Color();
    color.setRGB(255, 255, 255);
    colors.push(color.r, color.g, color.b);
  }
  starsGeometry.setAttribute(
    "position",
    new THREE.Float32BufferAttribute(positions, 3)
  );
  starsGeometry.setAttribute(
    "color",
    new THREE.Float32BufferAttribute(colors, 3)
  );
  //星辰材质
  let starsMaterial = new THREE.PointsMaterial({
    map: new THREE.TextureLoader().load(starImg),
    size: 4,
    blending: THREE.AdditiveBlending,
    fog: true,
    depthTest: false, //(不能与blending一起使用)
    // depthWrite: false, //(深度写入)防止星辰在球体前面出现黑块
  });
  //星辰的集合
  let starsMesh = new THREE.Points(starsGeometry, starsMaterial);
  starsMesh.scale.set(1000, 1000, 1000); //设置集合体范围
  starsMesh.name = "星辰";
  scene.add(starsMesh);
};

//创建光源
function createLight() {
  let lightColor = new THREE.Color(0xffffff);
  let ambient = new THREE.AmbientLight(lightColor); //环境光
  ambient.name = "环境光";
  scene.add(ambient);
  //   let pointLight = new THREE.PointLight(lightColor, 2, 1, 0); //点光源
  //   pointLight.visible = true;
  //   pointLight.position.set(0, 0, 1000); //点光源在原点充当太阳
  //   pointLight.name = "点光源";
  //   scene.add(pointLight); //点光源添加到场景中
  let directionalLight1 = new THREE.DirectionalLight(lightColor);
  directionalLight1.position.set(0, 0, 1000);
  scene.add(directionalLight1); //平行光源添加到场景中
  let directionalLight2 = new THREE.DirectionalLight(lightColor);
  directionalLight2.position.set(0, 0, -1000);
  scene.add(directionalLight2); //平行光源添加到场景中
  let directionalLight3 = new THREE.DirectionalLight(lightColor);
  directionalLight3.position.set(1000, 0, 0);
  scene.add(directionalLight3); //平行光源添加到场景中
  let directionalLight4 = new THREE.DirectionalLight(lightColor);
  directionalLight4.position.set(-1000, 0, 0);
  scene.add(directionalLight4); //平行光源添加到场景中
  let directionalLight5 = new THREE.DirectionalLight(lightColor);
  directionalLight5.position.set(0, 1000, 0);
  scene.add(directionalLight5); //平行光源添加到场景中
  let directionalLight6 = new THREE.DirectionalLight(lightColor);
  directionalLight6.position.set(0, -1000, 0);
  scene.add(directionalLight6); //平行光源添加到场景中
};

//创建球体
function createSphere(data: any) {
  let earthSize = 70; //地球尺寸
  let earthGroup = new THREE.Group(); //地球的组
  let earthGeometry = new THREE.SphereGeometry(earthSize, 100, 100); //地球几何体
  let nightColor = new THREE.Color(0x999999);
  let dayColor = new THREE.Color(0x444444);
  //地球材质
  let earthMaterial = new THREE.MeshPhongMaterial({
    map: new THREE.TextureLoader().load(
      mapImg
    ),
  });
  let earthMesh = new THREE.Mesh(earthGeometry, earthMaterial); //地球网格
  scene.add(earthMesh)
  // 发光地球
  let lightTexture = new THREE.TextureLoader().load(earthImg);
  let lightEarthGeometry = new THREE.SphereGeometry(earthSize, 100, 100);
  let lightEarthMaterial = new THREE.MeshBasicMaterial({
    map: lightTexture,
    alphaMap: lightTexture,
    blending: THREE.AdditiveBlending,
    transparent: true,
  });
  let lightEarth = new THREE.Mesh(lightEarthGeometry, lightEarthMaterial);
  scene.add(lightEarth);
  earthMesh.name = "地球";
  earthGroup.add(earthMesh); //将地球网格添加到地球组中
  earthGroup.name = "地球组";
  scene.add(earthGroup);
  //添加地球内外发光精灵
  let spriteTexture = new THREE.TextureLoader().load(glow);
  let spriteMaterial = new THREE.SpriteMaterial({
    map: spriteTexture,
    color: 0x4d76cf,
    transparent: true,
    depthWrite: false,
    depthTest: false,
    blending: THREE.AdditiveBlending,
  });
  let sprite = new THREE.Sprite(spriteMaterial);
  // sprite.scale.set(155, 155, 0);
  scene.add(sprite);

  // 内发光
  let spriteTexture1 = new THREE.TextureLoader().load(innerGlow);
  let spriteMaterial1 = new THREE.SpriteMaterial({
    map: spriteTexture1,
    color: 0x4d76cf,
    transparent: true,
    depthWrite: false,
    depthTest: false,
    blending: THREE.AdditiveBlending,
  });
  let sprite1 = new THREE.Sprite(spriteMaterial1);
  // sprite1.scale.set(128, 128, 0);
  scene.add(sprite1);
  let scale = new THREE.Vector3(1, 1, 1);
  createVirus(data, earthSize); //创建球面病毒
};

//经纬度坐标变换（传入e:纬度、a经度、t球半径、o球额外距离）
function latLongToVector3(e: any, a: any, t: any, o: any) {
  var r = (e * Math.PI) / 180,
    i = ((a - 180) * Math.PI) / 180,
    n = -(t + o) * Math.cos(r) * Math.cos(i),
    s = (t + o) * Math.sin(r),
    l = (t + o) * Math.cos(r) * Math.sin(i);
  return new THREE.Vector3(n, s, l); //计算三维向量
};

//创建病毒
function createVirus(data: any, earthSize: any) {
  let colors = [
    new THREE.Color(0xf9b8b8),
    new THREE.Color(0xfe4242),
    new THREE.Color(0xff0000),
  ]; //病毒颜色列表
  let virSize = 4; //病毒大小
  let pointsGroup = new THREE.Group(); //点的组
  let list = JSON.parse(JSON.stringify(data));
  list.forEach((e: {
    value: number; color: any; position: any[];
  }) => {
    e.value >= 10000000 && (e.color = colors[2]); //根据病毒数赋予不同颜色
    e.value >= 500000 && e.value < 10000000 && (e.color = colors[1]);
    e.value < 500000 && (e.color = colors[0]);
    if (e.position) {
      let virusMaterial = new THREE.SpriteMaterial({
        color: e.color,
        map: new THREE.TextureLoader().load(virusImg),
        side: THREE.FrontSide, //只显示前面
      }); //病毒材质
      let Sprite = new THREE.Sprite(virusMaterial); //点精灵材质
      Sprite.scale.set(virSize, virSize, 1); //点大小
      let lat = e.position[1]; //纬度
      let lon = e.position[0]; //经度
      let s = latLongToVector3(lat, lon, earthSize, 1); //坐标转换
      Sprite.position.set(s.x, s.y, s.z); //设置点的位置
      Sprite.dotData = e; //将点的数据添加到dotData属性中
      Sprite.name = "病毒";
      pointsGroup.add(Sprite); //添加进点的组中
    }
  });
  pointsGroup.name = "病毒组";
  scene.add(pointsGroup); //点的组添加到旋转组中
};

//创建鼠标控件
function createOrbitControls() {
  orbitControls = new OrbitControls(camera, renderer.domElement);
  orbitControls.enablePan = false; //右键平移拖拽
  orbitControls.enableZoom = true; //鼠标缩放
  orbitControls.enableDamping = true; //滑动阻尼
  orbitControls.dampingFactor = 0.05; //(默认.25)
  orbitControls.minDistance = 150; //相机距离目标最小距离
  orbitControls.maxDistance = 500; //相机距离目标最大距离
  orbitControls.autoRotate = true; //自转(相机)
  orbitControls.autoRotateSpeed = 1; //自转速度
  orbitControls.enableRotate = true; //鼠标左键控制旋转
};

//渲染
function render() {
  anId.value = requestAnimationFrame(render);
  document.getElementById("sphereDiv") &&
    document
      .getElementById("sphereDiv")!
      .addEventListener("mousemove", onMousemove, false);

  orbitControls.update(); //鼠标控件实时更新
  renderer.render(scene, camera);
};

//鼠标移动事件(光线投射器不要放在vue的data中，会卡顿)
function onMousemove(e: any) {
  let dom = document.getElementById("sphereDiv");
  let width = dom!.clientWidth; //窗口宽度
  let height = dom!.clientHeight; //窗口高度
  //将鼠标点击位置的屏幕坐标转换成threejs中的标准坐标
  mouse.x = (e.offsetX / width) * 2 - 1;
  mouse.y = -(e.offsetY / height) * 2 + 1;
  raycaster.setFromCamera(mouse, camera); //通过鼠标点的位置和当前相机的矩阵计算出raycaster
  let intersects = raycaster.intersectObjects(scene.children); // 获取raycaster直线与网格列表相交的集合
  if (intersects.length !== 0 && intersects[0].object.name == "病毒") {
    (isTag) && (currentPointData.value = intersects[0].object.dotData); //intersects列表是按照距离屏幕距离排序的，第一个距屏幕最近
    dom!.style.cursor = "pointer"; //光标样式
    position.value = {
      x: e.pageX + 20 + "px",
      y: e.pageY + "px",
    }; //获取标签位置
  } else {
    currentPointData.value = {}; //置空标签数据
    dom!.style.cursor = "move"; //光标样式
  }
};

//初始化图表
function initEchart() {
  let sortList: any = sortFun(sphereData.value); //球体数据排序
  top50worldList.data = sortFun(sphereData.value).slice(0, sliceNum);
  console.log(top50worldList)
  //histogramChartFun(sortList.slice(0, sliceNum)); //绘制国家排名柱状图
  let sortList1: any = allData.value.list;
  sortList1.sort(sortValue)
  sortList1 = sortList1.splice(0, 3)
  // console.log(sortList1, 'nnnn')
  drawEchart(sortList1)
  certain.value = Number(allData.value.othertotal.certain); //获取确诊值
  addcure.value = Number(allData.value.othertotal.recure); ///获取治愈值
  addDie.value = Number(allData.value.othertotal.die); ///获取死亡值
}
//排序
function sortValue(a: any, b: any) {
  return b.value - a.value
}
//排序(冒泡法)
function sortFun(arr: any) {
  arr.forEach((a: any, index: number) => {
    for (let j = 0; j < arr.length - 1 - index; j++) {
      if (Number(arr[j].value) < Number(arr[j + 1].value)) {
        let tmp = arr[j];
        arr[j] = arr[j + 1];
        arr[j + 1] = tmp;
      }
    }
  });
  return arr;
};
function drawEchart(list: any) {
  let chartDom = document.getElementById("histogramDiv1");
  (histogramChart) && (histogramChart.dispose());
  histogramChart = echarts.init(chartDom);
  let option: any = {
    tooltip: {
      trigger: 'axis',
      axisPointer: {
        type: 'shadow'
      }
    },
    grid: {
      top: '15%',
      right: '10%',
      left: '20%',
      bottom: '12%'
    },
    xAxis: [{
      type: 'category',
      data: [],
      axisLine: {
        lineStyle: {
          color: 'rgba(255,255,255,0.12)'
        }
      },
      axisLabel: {
        margin: 10,
        color: '#e2e9ff',
        textStyle: {
          fontSize: 14
        },
      },
    }],
    yAxis: [{
      name: '单位：人',
      axisLabel: {
        formatter: '{value}',
        color: '#e2e9ff',
      },
      axisLine: {
        show: false,
        lineStyle: {
          color: 'rgba(255,255,255,1)'
        }
      },
      splitLine: {
        lineStyle: {
          color: 'rgba(255,255,255,0.12)'
        }
      }
    }],
    series: [{
      type: 'bar',
      data: [],
      barWidth: '20px',
      itemStyle: {
        normal: {
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
            offset: 0,
            color: 'rgba(0,244,255,1)' // 0% 处的颜色
          }, {
            offset: 1,
            color: 'rgba(0,77,167,1)' // 100% 处的颜色
          }], false),
          barBorderRadius: [30, 30, 30, 30],
          shadowColor: 'rgba(0,160,221,1)',
          shadowBlur: 4,
        }
      },
      label: {
        normal: {
          show: true,
          lineHeight: 30,
          width: 90,
          height: 30,
          backgroundColor: 'rgba(0,160,221,0.1)',
          borderRadius: 200,
          position: ['-8', '-60'],
          distance: 1,
          formatter: [
            '    {d|●}',
            ' {a|{c}}     \n',
            '    {b|}'
          ].join(','),
          rich: {
            d: {
              color: '#3CDDCF',
            },
            a: {
              color: '#fff',
              align: 'center',
            },
            b: {
              width: 1,
              height: 30,
              borderWidth: 1,
              borderColor: '#234e6c',
              align: 'left'
            },
          }
        }
      }
    }]
  }
  list.forEach((item: any) => {
    option.xAxis[0].data.push(item.name)
    option.series[0].data.push(item.value)
  })
  console.log(option)
  option && histogramChart.setOption(option);
}
//获取用户ip信息
function getLocationMsg() {
  if (process.env.NODE_ENV !== "production") {
    let jsonpUrl: any = process.env.VUE_APP_3;
    jsonp(jsonpUrl, (res: any) => {
      userMsg.value = res;
      getProvinceData(); //获取当前省份数据
      getWeather();
    })
  } else {
    userMsg.value = tempIpData;
    getProvinceData(); //获取当前省份数据
  };

};
//获取当前地区的天气
function getWeather() {
  if (process.env.NODE_ENV !== "production") {
    let jsonpUrl: any = process.env.VUE_APP_6;
    createServe(jsonpUrl).then(res => {
      weather.value = res.data
    })

  } else {
    // weather.value = tempIpData;

  };
}
//获取当前省数据
function getProvinceData() {
  let pro = userMsg.value.pro; //当前省
  let ePro = ""; //英文省名
  //开发环境用临时数据
  if (process.env.NODE_ENV !== "production") {
    //遍历获取到英文名
    allData.value.list.forEach((l: any) => {
      if (pro.search(l.name) >= 0) {
        ePro = l.ename;
      }
    })
    //jsonp获取到当前省数据
    jsonp1(
      process.env.VUE_APP_5,
      (res: any) => {
        currentProvinceData.value = res.data; //获取到当前省数据
      },
      "val1",
      "mod=province&province=" + ePro
    );
  } else {
    currentProvinceData.value = tempProvinceData.data;
  }
}

//点击省内分析
function provinceAnalyze() {
  isProvinceEchartDrawer.value = true;
}

//确认下载报告
function sureDownloadReport() {
  ElMessageBox.confirm(
    '确认下载当地疫情报告？', {
    confirmButtonText: '确定',
    cancelButtonText: '取消',
  }
  )
    .then(() => {
      downloadReport(); //下载报告
    })
    .catch(() => {
      ElMessage({
        type: 'info',
        message: '已取消下载！',
      })
    })
};

//下载本地疫情报告
async function downloadReport() {
  isLoading.value = true;
  let tempData: any = {}; //当前省的临时数据
  await (tempData = JSON.parse(JSON.stringify(currentProvinceData.value)));
  let currentDate: any = currentProvinceData.value.cachetime.split(" ")[0]; //当前时间
  let wordData: any = {
    wordName: "", //文件名
    hasCityData: true, //是否有当前城市
    currentDate: currentDate, //文件生成时间
    version: version.value, //系统版本
    currentCityData: {}, //当前城市数据
    overviewData: {
      name: tempData.province,
      contotal: tempData.contotal, //累计数
      econNum: tempData.econNum, //现存数
      curetotal: tempData.curetotal, //治愈数
      deathtotal: tempData.deathtotal, //死亡数
      conadd: tempData.conadd, //新增数
    },
    tabData: [], //表格数据
    sourceUrl: window.location.href //来源url
  };
  await tempData.city.forEach((c: any, index: any) => {
    c.index = index + 1; //添加序号
    wordData.tabData.push(c); //添加表格数据
    if (userMsg.value.city.search(c.name) !== -1) {
      wordData.hasCityData = true;
      wordData.currentCityData = c; //获取当前数据城市
      wordData.wordName = c.name; //获取文件名
    }
  })
  isLoading.value = false;
  //城市名未找到时将省名添加到文件名中
  if (!wordData.wordName) {
    wordData.hasCityData = false;
    wordData.wordName = wordData.overviewData.name;
  }
  // console.log(wordData);
  await exportWord("docx/word.docx", wordData, wordData.wordName + "疫情报告.docx");
  ElMessage({
    type: 'success',
    message: '报告下载成功！',
  })
}
</script>
<style scoped lang='scss'>
.container {
  user-select: none;
  height: 100%;
  width: 100%;

  .isMobile-div {
    position: absolute;
    height: 100vh;
    width: 100vw;
    top: 0px;
    right: 0px;
    z-index: 1000;
    background-color: #000;
  }

  .top-div {
    width: 100%;
    height: 50px;
    position: absolute;
    background-color: rgba(255, 255, 255, .2);
    display: flex;
    justify-content: space-between;

    .name-div {
      margin-left: 10px;

      h2,
      h4 {
        display: inline-block;
        margin: 10px 5px;
      }
    }

    .btn-div {
      margin: 10px;

      .btn {
        border: none;
        color: #fff;

        img {
          height: 25px;
        }
      }
    }

  }

  .header {
    position: absolute;
    height: 66px;
    left: 0px;
    right: 0px;
    top: 0px;
    background: url(../assets/images/header_bg.png);
    background: linear-gradient(180deg, rgba(0, 20, 37, 0.3) 0%, rgba(3, 57, 104, 0.3) 100%);
    box-shadow: inset 0px -1px 0px rgba(15, 143, 252, 0.6);

    .header_left {
      position: absolute;
      width: 20%;
      height: 100%;
      left: 0px;
      top: 0px;
      background: url(../assets/images/Vector\ 313.png) no-repeat;

      .header_left_jiao {
        position: absolute;
        left: 14px;
        top: 18.86px;
        width: 54px;
        height: 31.43px;
        background: linear-gradient(110.43deg, rgba(15, 143, 252, 0) 0.34%, #0F8FFC 99%);
        background: url(../assets/images/Vector\ 318.png);
      }

      .header_left_time {
        position: absolute;
        width: 150px;
        height: 32px;
        top: 15px;
        left: 100px;
      }

    }

    .header_mid {
      position: absolute;
      width: 60%;
      height: 100%;
      left: 50%;
      top: 0px;
      transform: translateX(-50%);

      .header_mid_top {
        position: absolute;
        width: 353.68px;
        height: 18.59px;
        top: 0;
        left: 50%;
        transform: translateX(-50%);
        background: url(../assets/images/Union\ \(5\).png) no-repeat;

      }

      .header_mid-text {
        position: absolute;
        width: 392px;
        height: 42px;
        left: 50%;
        top: 19px;
        transform: translateX(-50%);
        font-family: 'Hiragino Kaku Gothic StdN';
        font-style: normal;
        font-weight: 900;
        font-size: 28px;
        line-height: 42px;
        text-align: center;
      }

      .header_mid_bt {
        position: absolute;
        width: 100%;
        height: 18px;
        bottom: 0;
        left: 50%;
        transform: translateX(-50%);
        background: url(../assets/images/Union\ \(3\).png) no-repeat;
        background-size: 100%;
      }

      .header_mid_lline {
        position: absolute;
        width: 294px;
        height: 9px;
        left: 30px;
        top: 37px;
        background: linear-gradient(270deg, #0F8FFC 0.51%, rgba(15, 143, 252, 0) 103.91%);
        opacity: 0.8;
        background: url(../assets/images/Rectangle\ 793.png) no-repeat;
      }

      .header_mid_rline {
        position: absolute;
        width: 294px;
        height: 9px;
        right: 30px;
        top: 37px;
        background: linear-gradient(270deg, #0F8FFC 0.51%, rgba(15, 143, 252, 0) 103.91%);
        opacity: 0.8;
        background: url(../assets/images/Rectangle\ 794.png) no-repeat;
      }
    }

    .header_right {
      position: absolute;
      width: 20%;
      height: 100%;
      right: 0px;
      top: 0px;
      background: url(../assets/images/Vector\ 314.png) no-repeat;

      .header_right_jiao {
        position: absolute;
        right: 14px;
        top: 18.86px;
        width: 54px;
        height: 31.43px;
        background: linear-gradient(110.43deg, rgba(15, 143, 252, 0) 0.34%, #0F8FFC 99%);
        background: url(../assets/images/Vector\ 319.png);
      }

      .header_left_weather {
        position: absolute;
        width: 150px;
        height: 32px;
        top: 15px;
        right: 50px;
      }
    }
  }



  #sphereDiv {
    height: 100%;
    width: 100%;
    cursor: move;
  }

  .left_bg {
    height: calc(100vh - 66px);
    width: 494px;
    background: url(../assets/images/left.png) no-repeat;
    background-size: 100% 100%;
    position: absolute;
    left: 0;
    top: 70px;

    .sphereDataDiv {
      pointer-events: none;
      position: absolute;
      top: 20px;
      left: 0px;
      height: 49%;
      width: 400px;
      text-align: center;
      padding: 10px 20px;

      .sphereDataDiv_title {
        width: 80%;
        height: 30px;
        line-height: 30px;
        background: linear-gradient(90deg, rgba(100, 134, 175, 0.31) 69.01%, rgba(2, 36, 66, 0.01) 99.99%);
        font-size: 20px;
        font-weight: 900;
        border-radius: 10px;
        text-align: left;
        padding-left: 25px;
      }

      .histogramDivDiv {
        height: 100%;
        width: 100%;
        overflow-y: scroll;
        pointer-events: auto;
        margin-top: 10px;

        #histogramDiv {
          height: 1500px;
          width: 100%;
        }
      }

    }

    .sphereDataDiv1 {
      pointer-events: none;
      position: absolute;
      top: 450px;
      left: 0px;
      height: 45%;
      width: 400px;
      text-align: center;
      padding: 10px 20px;
      margin-top: 10px;

      .sphereDataDiv1_title {
        width: 80%;
        height: 30px;
        line-height: 30px;
        background: linear-gradient(90deg, rgba(100, 134, 175, 0.31) 69.01%, rgba(2, 36, 66, 0.01) 99.13%);
        font-size: 20px;
        font-weight: 900;
        border-radius: 10px;
        text-align: left;
        padding-left: 25px;
      }

      .histogramDivDiv1 {
        width: 100%;
        height: 90%;
        padding: 10px;

        #histogramDiv1 {
          height: 100%;
          width: 100%;
        }
      }
    }
  }

  .right_bg {
    height: calc(100vh - 66px);
    width: 494px;
    background: url(../assets/images/right.png) no-repeat;
    background-size: 100% 100%;
    position: absolute;
    right: 0;
    top: 70px;

    .numDiv {
      position: absolute;
      top: 20px;
      right: 0px;
      pointer-events: none;
      height: calc(100% - 50px);
      display: flex;
      flex-direction: column;
      height: 40%;
      width: 400px;
      padding: 10px 20px;

      .numDiv_title {
        width: 80%;
        height: 30px;
        line-height: 30px;
        background: linear-gradient(90deg, rgba(100, 134, 175, 0.31) 69.01%, rgba(2, 36, 66, 0.01) 99.13%);
        font-size: 20px;
        font-weight: 900;
        border-radius: 10px;
        text-align: left;
        padding-left: 25px;
      }

      .numDiv_border {
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: row;
        justify-content: space-around;
        padding: 10px;

        .addconDiv {
          width: 180px;
          height: 167px;
          background: url(../assets/images/certain.png) no-repeat;
          position: relative;

          .certain-div {
            position: absolute;
            bottom: 30px;
            left: 15px;
          }

          .tit {
            width: 50px;
            height: 30px;
            position: absolute;
            left: 50%;
            top: 50%;
            transform: translate(-50%,-50%);
          }
        }

        .addcureDiv {
          width: 180px;
          height: 167px;
          background: url(../assets/images/addcure.png) no-repeat;
          position: relative;

          .addcure-div {
            position: absolute;
            bottom: 30px;
            left: 15px;
          }

          .tit {
            width: 50px;
            height: 30px;
            position: absolute;
            left: 50%;
            top: 50%;
            transform: translate(-50%,-50%);
          }
        }

        .addDieDiv {
          width: 180px;
          height: 167px;
          background: url(../assets/images/addDie.png) no-repeat;
          position: relative;

          .addDie-div {
            position: absolute;
            bottom: 30px;
            left: 15px;
          }

          .tit {
            width: 50px;
            height: 30px;
            position: absolute;
            left: 50%;
            top: 50%;
            transform: translate(-50%,-50%);
          }
        }
      }
    }
  }


  .set-div {
    position: absolute;
    bottom: 0px;
    z-index: 999;
    margin: 0px 0px 20px 20px;

    i:hover {
      cursor: pointer;
      color: #fff;
    }
  }

}
</style>