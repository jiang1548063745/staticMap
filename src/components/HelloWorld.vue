<template>
  <div id="baseMap" ref="baseMap">
    
  </div>
</template>

<script>
import 'ol/ol.css';
import ImageLayer from 'ol/layer/Image'
import Map from 'ol/Map'
import Projection from 'ol/proj/Projection'
import Static from 'ol/source/ImageStatic'
import View from 'ol/View'
import {getCenter} from 'ol/extent'
import VectorSource from 'ol/source/Vector'
import VectorLayer from 'ol/layer/Vector'
// import Point from 'ol/geom/Point'
// import {transform} from 'ol/proj'

const extent = [0, 0, 1024, 968]

export default {
  name: 'Map',
  data() {
    return {
      mapObj: undefined,
      projection: new Projection({
        code: 'xkcd-image',
        units: 'pixels',
        extent: extent,
      }),
      vectorLayer: undefined
    }
  },
  mounted() {
    this.initMap()
    this.addVectorLayer()
    this.addEvent()
  },
  methods: {
    initMap() {
      this.mapObj = new Map({
        layers: [
          new ImageLayer({
            source: new Static({
              imageExtent: extent,
              projection: this.projection,
              // url: 'http://www.lushangeopark.com/system_dntb/upload/201864101334713.jpg'
              // url: 'staticMap.jpg',
              url: 'staticMap.svg'
            })
          })
        ],
        target: this.$refs.baseMap,
        view: new View({
          projection: this.projection,
          center: getCenter(extent),
          zoom: 2,
          maxZoom: 8
        })
      })
    },
    addVectorLayer() {
      this.vectorLayer = new VectorLayer({
        source: new VectorSource()
      })
      this.mapObj.addLayer(this.vectorLayer)
    },
    addEvent() {
      this.mapObj.on('singleclick', function(event) {
        // const lonlat = transform(event.coordinate, this.projection, 'EPSG:4326')
        // console.log(lonlat)
        console.log(event.coordinate)
        // const feature = new Point(event.coordinate)
        // console.log(feature)
        // this.vectorLayer.getSource().addFeature(feature)
        // 待测试添加点
      }) 
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#baseMap {
  width: 100%;
  height: calc(90vh);
}
</style>
