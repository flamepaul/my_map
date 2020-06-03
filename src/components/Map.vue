<template>
  <div>
    <!-- 2.为echarts准备一个有大小的盒子 -->
    <div ref="mapbox" style="width: 800px; height: 600px" ></div>
  </div>
</template>

<script>
//1.导入echarts
import echarts from "echarts";
//导入地图
import "echarts/map/js/china.js";
//导入jsonp
import jsonp from "jsonp";

//4.使用地图，需要先注册地图
const option = {
  series: [
    {
      name: "确诊人数",
      type: "map", //告诉echarts 要去渲染的是一个地图
      map: "china", //告诉echarts 要去渲染中国地图
      label: {
        //控制对应地区的汉字
        show: true,
        color: "#333", //控制地区名的字体颜色
        fontSize: 10
      },
      itemStyle: {
        //地图板块的颜色和边框
        areaColor: "#eee"
        // borderColor: 'blue'
      },
      roam: true, //鼠标滚动放大缩小
      zoom: 1.2, //控制地图的放大与缩小
      emphasis: {
        //控制鼠标滑过之后的背景色和字体颜色
        label: {
          color: "#fff",
          fontSize: 12
        },
        itemStyle: {
          areaColor: "#83b5e7"
        }
      },
      data: [] //用来展示后台给的数据的，从jsonp获得 里面的格式{name:xx,value:xx}
    }
  ],
  visualMap: [
    {
      type: "piecewise", //数值样式是连续还是分段（分段）
      show: true, //是否展示数值
      pieces: [
        //自定义分段数值
        { min: 10000 },
        { min: 1000, max: 9999 },
        { min: 100, max: 999 },
        { min: 10, max: 99 },
        { min: 1, max: 9 }
      ],
      //align:'right',//默认Left
      //orient:'horizontal'默认竖直
      inRange: {
        symbol: "rect", //形状
        color: ["#ffc0b1", "#9c0505"]
      },
      //方块大小
      itemWidth: 20,
      itemHeight: 10
    }
  ],
  tooltip: {
    //鼠标移动至位置时，显示
    trigger: "item"
  },
  toolbox: {
    //在旁边显示保存图片
    show: true,
    orient: "vertical",
    left: "right",
    top: "center",
    feature: {
      saveAsImage: {}
    }
  }
};

export default {
  name: "Map",
  //此时页面上的元素，已经被渲染完毕
  mounted() {
    this.getData();
    //3.基于引用，初始化echarts实例
    this.mychart = echarts.init(this.$refs.mapbox);
  },
  methods: {
    getData() {
      jsonp(
        "https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427",
        (err, data) => {
          if (!err) {
            //代表请求数据成功
            console.log(data);
            let list = data.data.list.map(item => ({
              name: item.name,
              value: item.value
            }));
            option.series[0].data = list;
              //5，展示数据
            this.mychart.setOption(option);
            //要想渲染出来，两个大前提，DOM元素渲染出来，数据获取到。
          }
        }
      );
    }
  }
};
</script>

<style scoped>
</style>