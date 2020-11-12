<template>
  <div id="mapContainer"></div>
</template>

<script>
import 'ol/ol.css'
import Projection from 'ol/proj/Projection'
import Map from 'ol/Map'
import View from 'ol/View'
import ImageLayer from 'ol/layer/Image'
import Static from 'ol/source/ImageStatic'
import VectorLayer from 'ol/layer/Vector'
import VectorSource from 'ol/source/Vector'
import Feature from 'ol/Feature'
import Point from 'ol/geom/Point'
import LineString from 'ol/geom/LineString'
import { Icon, Stroke, Style, Text, Fill } from 'ol/style'
import { getCenter } from 'ol/extent'

const extent = [0, 0, 1024, 968]

export default {
  data() {
    return {
      mapObj: undefined,
      projection: new Projection({
        code: 'xkcd-image',
        units: 'pixels',
        extent: extent,
      }),
      deviceLayer: undefined,
      trckLayer: undefined
    }
  },
  mounted() {
    this.initMap()
    this.initLayers()
    this.addDevicePoints()
    this.addTrak()
    window['clearTrak'] = this.clearTrak
  },
  methods: {
    deviceStyle(feature, resolution) {
      const name = feature.get('name')
      return new Style({
        image: new Icon({
          src: 'device.svg',
          scale: 0.9,
        }),
        text: new Text({
          textAlign: 'center',  
          offsetY: -20,
          font: '13px 微软雅黑',
          fill: new Fill({ color: '#2121EF' }),
          stroke: new Stroke({ color: '#ffffff', width: 2 }), 
          text: resolution < 80 ? name.trim() : ''
        })
      })
    },
    lineStyleFunction(feature) {
      const trackLine = feature.getGeometry()

      const styles = [
        new Style({
          stroke: new Stroke({
            color: '#2E8B57',
            width: 16
          })
        })
      ]

      trackLine.forEachSegment(function (start, end) {
        console.log(end)
        const dx = end[0] - start[0]
        const dy = end[1] - start[1]
        const rotation = Math.atan2(dy, dx)

        const markX = start[0] + end[0]
        const markY = start[1] + end[1]

        styles.push(
          new Style({
            geometry: new Point([markX / 2, markY / 2]),
            image: new Icon({
              src: 'arrow.png',
              anchor: [0.75, 0.5],
              rotateWithView: true,
              rotation: -rotation,
            })
          })
        )
      })

      return styles
    },
    // 初始化地图
    initMap() {
      this.mapObj = new Map({
        layers: [
          new ImageLayer({
            source: new Static({
              imageExtent: extent,
              projection: this.projection,
              url: 'staticMap.svg'
            })
          })
        ],
        target: document.getElementById('mapContainer'),
        view: new View({
          projection: this.projection,
          center: getCenter(extent),
          zoom: 2,
          maxZoom: 8
        })
      })
    },
    // 初始化设备/轨迹图层
    initLayers() {
      this.deviceLayer = new VectorLayer({
        source: new VectorSource(),
        style: this.deviceStyle
      })

      this.trckLayer = new VectorLayer({
        source: new VectorSource(),
        style: this.lineStyleFunction
      })

      this.mapObj.addLayer(this.trckLayer)
      this.mapObj.addLayer(this.deviceLayer)
    },
    // 添加设备点 在此处请求数据拼装设备数据
    addDevicePoints() {
      const features = []
      const freature_1 = new Feature({
        geometry: new Point([414.10003662109375, 707.7999877929688])
      })
      freature_1.set('name', '人脸抓拍摄像机')
      features.push(freature_1)
      
      const freature_2 = new Feature({
        geometry: new Point([458.10003662109375, 611])
      })
      freature_2.set('name', '人脸识别终端')
      features.push(freature_2)
      
      const freature_3 = new Feature({
        geometry: new Point([455.70001220703125, 319.79998779296875])
      })
      freature_3.set('name', '指纹识别器')
      features.push(freature_3)

      const freature_4 = new Feature({
        geometry: new Point([640.6277118509987, 308.16190381475894])
      })
      freature_4.set('name', '身份读卡器')
      features.push(freature_4)

      this.deviceLayer.getSource().addFeatures(features)
    },
    // 添加轨迹 在此处请求数据拼装轨迹数据
    addTrak() {
      const feature = new Feature({
        geometry: new LineString([
            [414.10003662109375, 707.7999877929688],
            [458.10003662109375, 611],
            [455.70001220703125, 319.79998779296875],
            [640.6277118509987, 308.16190381475894]
          ]
        )
      })
      this.trckLayer.getSource().addFeature(feature)
    },
    // 清楚轨迹
    clearTrak() {
      this.trckLayer.getSource().clear()
    }
  }
}
</script>

<style scoped>
#mapContainer {
  height: 100vh;
  width: 100%;
}
</style>