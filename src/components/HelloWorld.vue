
<template>
  <div id="mapView">
    <div id="viewDiv"></div>
    <div id="info1" style="height:150px;width:300px;"></div>
  </div>
</template>

<script lang="ts" setup>
import { nextTick, onMounted } from 'vue'
import Map from '@arcgis/core/Map'
import MapView from '@arcgis/core/views/MapView'
import * as echarts from 'echarts'

const echartsInfos = [] as any

let echartsInfo = {} as any

onMounted(() => {
  const map = new Map({
    basemap: 'hybrid'
  })

  const view = new MapView({
    map: map,
    container: 'viewDiv',
    center: [120, 30],
    zoom: 8
  })
  view.when(function () {
    view.watch('extent', function () {
      relocatePopup()
    })
    view.watch('rotation', function () {
      relocatePopup()
    })

    nextTick(() => {
      echartsMapInit()
    })
  })

  function relocatePopup () {
    const mapPoint = {
      x: echartsInfo.x,
      y: echartsInfo.y,
      // spatialReference: view.spatialReference
      spatialReference: {
        wkid: 4326
      }
    } as any
    const screenPoint = view.toScreen(mapPoint)
    const obj = {} as any
    obj.x = screenPoint.x
    obj.y = screenPoint.y
    obj.option = echartsInfo.option
    obj.id = echartsInfo.id
    obj.echartsObj = echartsInfo.echartsObj

    positionEchartsMap(obj)
  }

  function positionEchartsMap (obj: any) {
    document.getElementById(obj.id)!.style.transform =
      'translate3d(' + obj.x + 'px, ' + obj.y + 'px, 0)'
    switch (view.zoom) {
      case 0:
      case 1:
      case 2:
      case 3:
      case 4:
      case 5:
        document.getElementById(obj.id)!.style.height = '50px'
        document.getElementById(obj.id)!.style.width = '50px'
        break
      case 6:
      case 7:
      case 8:
        document.getElementById(obj.id)!.style.height = '120px'
        document.getElementById(obj.id)!.style.width = '200px'
        break
      case 9:
      case 10:
        document.getElementById(obj.id)!.style.height = '150px'
        document.getElementById(obj.id)!.style.width = '300px'
        break
      case 11:
      case 12:
        document.getElementById(obj.id)!.style.height = '200px'
        document.getElementById(obj.id)!.style.width = '350px'
        break
      default:
        document.getElementById(obj.id)!.style.height = '250px'
        document.getElementById(obj.id)!.style.width = '400px'
    }
    if (obj.echartsObj) {
      obj.echartsObj.resize()
    }
  }

  function echartsMapInit () {
    echartsInfo = {
      x: 104.072043,
      y: 30.663724,
      id: 'info1',
      echartsObj: null,
      option: {
        color: ['#3398DB'],
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'shadow'
          }
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          top: '3%',
          containLabel: true
          // y2: 140
        },
        xAxis: [
          {
            type: 'category',
            data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
            axisTick: {
              alignWithLabel: true
            },
            axisLabel: {
              interval: 0,
              rotate: -30
            },
            axisLine: {
              lineStyle: {
                color: '#FFFFFF',
                width: 1
              }
            }
          }
        ],
        yAxis: [
          {
            type: 'value',
            splitLine: {
              lineStyle: {
                color: ['#0087ED']
              }
            },
            nameTextStyle: {
              color: ['#FFFFFF']
            },
            axisLine: {
              lineStyle: {
                color: '#FFFFFF',
                width: 1
              }
            }
          }
        ],
        series: [
          {
            name: '直接访问',
            type: 'bar',
            barWidth: '60%',
            data: [10, 52, 200, 334, 390, 330, 220]
          }
        ]
      }

    }

    const myChart = echarts.init(document.getElementById(echartsInfo.id)!)
    myChart.setOption(echartsInfo.option)

    positionEchartsMap(echartsInfo) // 调整图表位置及大小
  }
})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
#MapView {
  position: relative;
  width: 100%;
  height: 100%;
}
#viewDiv {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
}
</style>
