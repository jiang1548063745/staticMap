/**
 * 坐标拾取辅助器
 */
<template>
  <div id="mapContainer">
    <img id="overlay" src="https://webapi.amap.com/theme/v1.3/markers/b/mark_bs.png" style="width: 19px; height: 33px; top: 0px; left: 0px;">
    <div class="tool-box">
      <div class="box-header">
        <h5 class="title">坐标拾取</h5>
      </div>
      <div style="margin-top: 20px">
        <div class="row">
          <span class="col-sm-2 input-group-addon" style="margin-left: 10px">Lon:</span>
          <div class="col-sm-9">
            <input type="text" v-model="lonlat[0]" class="form-control" id="lon" placeholder="">
          </div>
        </div>
        <div class="row" style="margin-top: 10px">
          <span for="lat" class="col-sm-2 control-label" style="margin-left: 10px">Lat:</span>
          <div class="col-sm-9">
            <input type="text" v-model="lonlat[1]" class="form-control" id="lat" placeholder="">
          </div>
        </div>
        <button type="" @click="addMapEvt" class="btn btn-primary" style="display: block; margin-top: 10px; margin-left: auto; margin-right: auto">
          点击拾取坐标
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import 'ol/ol.css'
import Projection from 'ol/proj/Projection'
import Map from 'ol/Map'
import View from 'ol/View'
import ImageLayer from 'ol/layer/Image'
import Static from 'ol/source/ImageStatic'
import { getCenter } from 'ol/extent'
import { unByKey } from 'ol/Observable'
import Overlay from 'ol/Overlay'

const extent = [0, 0, 1024, 968]

export default {
  name: 'coordPickerup',
  data() {
    return{
      lonlat: [],
      map: {
        projection: new Projection({
          code: 'xkcd-image',
          units: 'pixels',
          extent: extent,
        }),
        moveEvtKey: null,
        clickEvtKey: null,
        overlay: null,
        borderLayer: null,
        zoom: 13,
        mapCenter: null,
        mapObj: null
      }
    }
  },
  mounted() {
    this.initMap()
    this.addOverlay()
    this.addMapEvt()
  },
  methods: {
    // 初始化地图
    initMap() {
      this.map.mapObj = new Map({
        layers: [
          new ImageLayer({
            source: new Static({
              imageExtent: extent,
              projection: this.map.projection,
              url: 'staticMap.jpg'
            })
          })
        ],
        target: document.getElementById('mapContainer'),
        view: new View({
          projection: this.map.projection,
          center: getCenter(extent),
          zoom: 2,
          maxZoom: 8
        })
      })
    },
    // 添加overlay
    addOverlay() {
      this.map.overlay = new Overlay({
        element: document.getElementById('overlay'),
        positioning: 'bottom-center',
        stopEvent: false
      })
      this.map.overlay.setPosition(getCenter(extent))
      this.map.mapObj.addOverlay(this.map.overlay)
    },
    // 添加事件
    addMapEvt() {
      let _this = this
      // 鼠标移动事件
      this.map.moveEvtKey = this.map.mapObj.on('pointermove', (e) => {
        _this.map.overlay.setPosition(e.coordinate)
      })

      // 鼠标点击事件
      this.map.clickEvtKey = this.map.mapObj.on('click', (e) => {
        _this.map.overlay.setPosition(e.coordinate)
        _this.lonlat = e.coordinate
        unByKey(_this.map.moveEvtKey)
        unByKey(_this.map.clickEvtKey)
        _this.map.moveEvtKey = null
        _this.map.clickEvtKey = null
      })
    }
  }
}
</script>

<style scoped>
#mapContainer {
  height: calc(100vh);
  width: 100%;
}
.tool-box {
  width: 250px;
  height: 210px;
  position: absolute;
  color: rgba(0,0,0,0.65);
  background-color: #FDFDFE;
  z-index: 99;
  margin-top: 20px;
  margin-left: 80%;
  border: 1px solid #e8e8e8;
  border-radius: 2px;
  box-sizing: border-box;
}
.box-header {
  height: 40px;
  border-bottom: 1px solid #e8e8e8;
}
.title {
  display: inline-block;
  -webkit-box-flex: 1;
  -webkit-flex: 1;
  -ms-flex: 1;
  flex: 1;
  padding: 9px 10px;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  color: #0d1a26;
  font-weight: 500;
  font-family: Avenir,-apple-system,BlinkMacSystemFont,'Segoe UI','PingFang SC','Hiragino Sans GB','Microsoft YaHei','Helvetica Neue',Helvetica,Arial,sans-serif,'Apple Color Emoji','Segoe UI Emoji','Segoe UI Symbol',sans-serif;
}
</style>