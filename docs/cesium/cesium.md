## first

```javascript
/**初始化cesium地图*/
  initMap() {
      //默认加载cesium底图，有时候访问失效会造成地图丢失、更改不加载底图
    const createColorCanvas = function (color: any) {
      let width = 1,
        height = 1;
      let canvas = document.createElement("canvas");
      canvas.width = width;
      canvas.height = height;
      let ctx = canvas.getContext("2d");
      ctx!.fillStyle = color;
      ctx!.fillRect(0, 0, width, height);
      return canvas.toDataURL();
    };
    Cesium.Ion.defaultAccessToken =""//官网申请一个token
      
    let viewer = new Viewer("cesium-container", {
      shouldAnimate: true,
      homeButton: false, //是否显示Home按钮
      fullscreenButton: false, //是否显示全屏按钮
      baseLayerPicker: false, //隐藏默认底图选择控件
      imageryProvider: new Cesium.SingleTileImageryProvider({
        url: createColorCanvas("#FFF"),
        rectangle: Cesium.Rectangle.fromDegrees(-180.0, -90.0, 180.0, 90.0),
      }),
      geocoder: false, //是否显示地名查找控件
      timeline: false, //是否显示时间线控件
      sceneModePicker: false, //是否显示投影方式控件
      navigationHelpButton: false, //是否显示帮助信息控件
      infoBox: false, //是否显示点击要素之后显示的信息
      requestRenderMode: true, //启用请求渲染模式
      scene3DOnly: false, //每个几何实例将只能以3D渲染以节省GPU内存
      sceneMode: 3, //初始场景模式 1 2D模式 2 2D循环模式 3 3D模式  SceneMode
      fullscreenElement: document.body, //全屏时渲染的HTML元素 暂时没发现用处
      selectionIndicator: false,
      animation: false, //是否显示动画控件
    });
    // 去除版权信息
    (viewer.cesiumWidget.creditContainer as any).style.display = "none";
    (viewer.container as any).style.cursor = "pointer";

    //1.Cesium加载高德矢量地图
    let layer = new Cesium.UrlTemplateImageryProvider({
      url: "http://webrd02.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=8&x={x}&y={y}&z={z}",
      minimumLevel: 4,
      maximumLevel: 18,
    });
    viewer.imageryLayers.addImageryProvider(layer);
    //2.Cesium加载高德影像
    // let imgLayer = new UrlTemplateImageryProvider({
    //   url: "https://webst02.is.autonavi.com/appmaptile?style=6&x={x}&y={y}&z={z}",
    //   minimumLevel: 3,
    //   maximumLevel: 18,
    // });
    // viewer.imageryLayers.addImageryProvider(imgLayer);
    //3.Cesium加载注记要素
    let annLayer = new Cesium.UrlTemplateImageryProvider({
      url: "http://webst02.is.autonavi.com/appmaptile?x={x}&y={y}&z={z}&lang=zh_cn&size=1&scale=1&style=8",
      minimumLevel: 3,
      maximumLevel: 18,
    });

    (viewer.container as any).style.cursor = "pointer";
    viewer.imageryLayers.addImageryProvider(annLayer);
    return viewer;
  },
```

## second

```javascript
/**生成航点*/
  create_point() {
      entities.add({
        name: "",
        id: '',//cesium每一个实体都有唯一的id,添加已存在的id会报错
        position: Cartesian3.fromDegrees(
          Number(position.lng),
          Number(position.lat),
          Number(position.alt) || 0
        ),
        point: {
          pixelSize: 24,
          color: Color.BLUEVIOLET,
          // fillColor: Color.RED,
        },
        label: {
          text: '',
          font: " 40px ",
          fillColor: Color.WHITE,
          horizontalOrigin: VerticalOrigin.CENTER as any,
          verticalOrigin: VerticalOrigin.CENTER,
          disableDepthTestDistance: Number.POSITIVE_INFINITY,
          showBackground: false,
          outlineWidth: 0,
        },
  },
```

