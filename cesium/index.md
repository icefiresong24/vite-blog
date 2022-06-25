### 添加实体

```JavaScript
var viewer = new Cesium.Viewer('cesiumContainer');//获取当前视图
//添加立方体
flyToPoint(viewer:any,lon:number,lat:number,height?:number){
        const f = viewer.entities.add({
            position: Cesium.Cartesian3.fromDegrees(
                Number(lon),
                Number(lat)
            ),
            billboard: {
                image: require("./1.png"),
                width: 30,
                height: 30,
                distanceDisplayCondition: new Cesium.DistanceDisplayCondition(0),
                disableDepthTestDistance: Number.POSITIVE_INFINITY,
            },
        })
//添加椭圆

        viewer.camera.flyTo({
            destination: Cesium.Cartesian3.fromDegrees(
                Number(lon),
                Number(lat),
                //120.24235441789206,
                //33.35542881718012,
                10000
            ),
        })
    }
```

```JavaScript
export function createPoint(viewer, { position, image, title }) {
  let point = viewer.entities.add({
    name: 'point',
    position,
    billboard: {
      image,
      width: 30,
      height: 30,
      distanceDisplayCondition: new Cesium.DistanceDisplayCondition(0),
      disableDepthTestDistance: Number.POSITIVE_INFINITY,
    },
    label: {
      text: title,
      font: '14pt monospace',
      style: Cesium.LabelStyle.FILL_AND_OUTLINE,
      outlineWidth: 2,
      verticalOrigin: Cesium.VerticalOrigin.BOTTOM,
      pixelOffset: new Cesium.Cartesian3(0.0, 32, 0.0),
      disableDepthTestDistance: Number.POSITIVE_INFINITY,
    },
  })
  return point
}

export function createLine(
  viewer,
  { polyCenter, positions, width, color, title }
) {
  let line = viewer.entities.add({
    position: polyCenter,
    polyline: {
      positions,
      width,
      material: Cesium.Color.fromCssColorString(color),
    },
    label: {
      // @ts-ignore
      text: title,
      font: '14pt monospace',
      style: Cesium.LabelStyle.FILL_AND_OUTLINE,
      outlineWidth: 2,
      distanceDisplayCondition: new Cesium.DistanceDisplayCondition(0),
      disableDepthTestDistance: Number.POSITIVE_INFINITY,
      verticalOrigin: Cesium.VerticalOrigin.BOTTOM,
      pixelOffset: new Cesium.Cartesian2(0, 0),
    },
  })
  return line
}

export function createPolygon(
  viewer,
  { polyCenter, outlineColor, positions, title, color }
) {
  var polygon = viewer.entities.add({
    position: polyCenter,
    polygon: {
      hierarchy: positions,
      material: Cesium.Color.fromCssColorString(color),
      outline: true,
      outlineWidth: 1,
      outlineColor: Cesium.Color.fromCssColorString(outlineColor),
    },
    label: {
      text: title,
      font: '14pt monospace',
      style: Cesium.LabelStyle.FILL_AND_OUTLINE,
      outlineWidth: 2,
      disableDepthTestDistance: Number.POSITIVE_INFINITY,
      verticalOrigin: Cesium.VerticalOrigin.BOTTOM,
      pixelOffset: new Cesium.Cartesian2(0, 0),
    },
  })
  return polygon
}
```

```JavaScript
 async handleAirLinePreview(item) {
      // let cesium_layer = this.g_cesium_layer();
      // let viewer = cesium_layer.viewer();
      let viewer = window.viewer;
      let res = await API.TASK.JingQingTrack({
        taskId: item.taskId,
      });
      if (res?.flightCourseJson) {
        let polyline = JSON.parse(res.flightCourseJson);
        console.log(polyline);
        let positions = [];
        polyline.points.forEach((item, index) => {
          console.log(index);
          positions.push(item.lon, item.lat, item.alt);
          console.log(positions);
          viewer.entities.add({
            position: Cesium.Cartesian3.fromDegrees(item.lon, item.lat),
            point: {
              pixelSize: 20,
              color: Cesium.Color.RED,
              fillColor: Cesium.Color.RED,
              heightReference: Cesium.HeightReference.RELATIVE_TO_GROUND,
            },
           
            label: {
              text: String(index + 1),
              scale: 0.5,
              font: "bold 24px Microsoft YaHei",
              fillColor: Cesium.Color.WHITE,
              horizontalOrigin: Cesium.VerticalOrigin.CENTER,
              verticalOrigin: Cesium.VerticalOrigin.CENTER,
              disableDepthTestDistance: Number.POSITIVE_INFINITY,
              showBackground: false,
              outlineWidth: 0,
            },
          });
        });
        console.log(positions);

        let redLine = viewer.entities.add({
          name: "Red line on terrain",
          polyline: {
            positions: Cesium.Cartesian3.fromDegreesArrayHeights(positions),
            width: 5,
            material: Cesium.Color.RED,
            clampToGround: true,
          },
        });
        viewer.flyTo(redLine);
      }
    },
```

### 删除实体

```JavaScript
//创建viewer
 
var viewer = new Cesium.Viewer('cesiumContainer');
 
//添加实体
 
var boxEntity = {
        id:'Box',
        position : Cesium.Cartesian3.fromDegrees(108, 34,0),
        box : {
            dimensions : new Cesium.Cartesian3(4000, 3000, 5000),
            material : Cesium.Color.RED.withAlpha(0.5),
            outline : true,
            outlineColor : Cesium.Color.BLACK
        }
    }
 var redBox = viewer.entities.add(boxEntity);
 
//获取实体
 
var getByIdBox = viewer.entities.getById('Box');
 
//删除添加的实体
 
错误方法：viewer.entities.remove(boxEntity);
 
正确方法：
 //方法一(针对性删除某一个)
 viewer.entities.remove(redBox);
        
 //方法二（通过id删除）
  viewer.entities.remove(getByIdBox);
 
 //方法三（删除所有实体）
 viewer.entities.removeAll();
```

### 屏幕控制：鼠标移动的事件、鼠标左键点击事件

```JavaScript
#var handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas);
//创建了一个屏幕控制实例，控制cesium的canvas

//#调用setInputAction，从英文上就能看出这是设置输入行为

//handler.setInputAction(function(x){y},事件类型ScreenSpaceEventType );

//这里注意:移动是movement，移动终点的位置是movement.endPosition;点击是click，点击的位置是click.position
//鼠标移动的事件
handler.setInputAction(function(movement){
　　//获取鼠标位置，camera.pickEllipsoid()返回一个cartesian类型位置
    let click_position =  viewer.scene.camera.pickEllipsoid(movement.endPosition,my_ellipsoid);
　　//位置数据转换只地理数据类型
    let carto_position = my_ellipsoid.cartesianToCartographic(click_position);
　　//cesium函数转换至地理数据类型的经纬度    
　　let longitude_x = Cesium.Math.toDegrees(carto_position.longitude);
    let longitude_y = Cesium.Math.toDegrees(carto_position.latitude);
    //topDiv是html中的div
　　topDiv.innerHTML="点击坐标的位置为"+longitude_x+"经度，"+longitude_y+"纬度";
},Cesium.ScreenSpaceEventType.MOUSE_MOVE)//ScreenSpaceEventType 

//鼠标左键点击事件
handler.setInputAction(function(click){
    var click_position =  viewer.camera.pickEllipsoid(click.position,my_ellipsoid);//click换成movement也可实现，但是position不是endposition
    var carto_position = Cesium.Cartographic.fromCartesian(click_position);
    //var carto_position = my_ellipsoid.cartesianToCartographic(click_position);
    var longitude_x = Cesium.Math.toDegrees(carto_position.longitude).toFixed(3);
    var longitude_y = Cesium.Math.toDegrees(carto_position.latitude).toFixed(3);
    topDdv.innerHTML="点击坐标的位置为"+longitude_x+"经度，"+longitude_y+"纬度";},Cesium.ScreenSpaceEventType.LEFT_CLICK)

```



```JavaScript
//航线规划
    paintLinePlan(data) {
      if (viewer) {
        this.paintLinePlan1 = viewer.entities.add({
          id: `paintLinePlan1`,
          polyline: {
            positions: new Cesium.CallbackProperty(() => {
              let list = data.reduce((total, cur) => {
                let { lon, lat } = cur,
                  pos = total;
                if (lon != 0 && lat != 0) {
                  pos = total.concat([lon, lat]);
                }
                return pos;
              }, []);
              return Cesium.Cartesian3.fromDegreesArray(list);
            }, false),
            width: 3,
            material: Cesium.Color.fromCssColorString("#52CDFF"),
            clampToGround: true,
          },
        });
      }
    },
```

#### 获取两点距离

```JavaScript
// 航线预览
    async handleAirLinePreview(res) {
        // try {
        // let viewer = window.viewer;
        // if (airline_entitys && airline_entitys.length) {
        //     airline_entitys.forEach((item) => {
        //         viewer.entities.remove(item);
        //     });
        // }
        // let res = {};
        // air_line_preview.forEach((item) => {
        //     viewer.entities.remove(item);
        // });

        // 有航点高度和距离的代码
        console.log('uav', res);
        console.log('uav', f);
        if (res && res.length) {
            console.log('uav', 'success');
            let viewer = window.viewer;
            airline_entitys.forEach((item) => {
                viewer.entities.remove(item);
            });
            let positions = [];
            let center = [];
            let label_arr = [];
            let _this = this;
            res.forEach((item, index) => {
                positions.push(item.lon, item.lat, item.alt);
                label_arr.push(
                    Cesium.Cartesian3.fromDegrees(item.lon, item.lat, item.alt)
                );
                let point_entity = viewer.entities.add({
                    position: Cesium.Cartesian3.fromDegrees(
                        item.lon,
                        item.lat,
                        item.alt
                    ),
                    name: "show_airline_point",
                    point: {
                        pixelSize: 10,
                        color: Cesium.Color.fromCssColorString("#00A9A9"),
                    },
                    label: {
                        text: String(item.alt) + "m",
                        scale: 0.6,
                        font: "bold 28px Microsoft YaHei",
                        // fillColor: Cesium.Color.BLUE,
                        fillColor: Cesium.Color.fromCssColorString("#FF7F09"),
                        horizontalOrigin: Cesium.VerticalOrigin.CENTER,
                        verticalOrigin: Cesium.VerticalOrigin.CENTER,
                        disableDepthTestDistance: Number.POSITIVE_INFINITY,
                        showBackground: false,
                        outlineWidth: 0,
                        pixelOffset: new Cesium.Cartesian2(25, -10),
                    },
                });
                airline_entitys.push(point_entity);
                if (label_arr.length > 1) {
                    let before = label_arr[label_arr.length - 2];
                    let after = label_arr[label_arr.length - 1];

                    _this.uav_create_label(before, after);
                }
            });

            positions = Cesium.Cartesian3.fromDegreesArrayHeights(positions);

            let redLine = viewer.entities.add({
                name: "Red line on terrain",
                polyline: {
                    positions: new Cesium.CallbackProperty(() => {
                        return positions;
                    }, false),
                    width: 4,
                    // material: Cesium.Color.RED,
                    material: Cesium.Color.fromCssColorString("#00A9A9"),
                    // clampToGround: true,
                },
            });
            viewer.flyTo(redLine);
            airline_entitys.push(redLine);
        } else {
            this.$el_message("暂无航线", () => {}, "error");
        }
        // } catch (error) {
        //     console.log(error);
        //     this.$el_message("暂无航线", () => {}, "error");
        // }
    },
    // 显示距离
    create_label(before, after) {
        let viewer = window.viewer;
        let before_wgs84 = Utils.transformCartesian2WGS84(before);
        let after_wgs84 = Utils.transformCartesian2WGS84(after);

        let center_lng = (before_wgs84.lng + after_wgs84.lng) / 2;
        let cetner_lat = (before_wgs84.lat + after_wgs84.lat) / 2;
        let alt = (after_wgs84.alt + before_wgs84.alt) / 2;

        let position = Utils.transformWGS842Cartesian({
            lng: center_lng,
            lat: cetner_lat,
            alt: alt + 10,
        });

        let text = `${Cesium.Cartesian3.distance(before, after).toFixed(2)} m`;

        let label_entity = viewer.entities.add({
            id: `label_${before}`,
            position: position,
            label: {
                text: text,
                scale: 0.5,
                font: "bold 30px Microsoft YaHei",
                fillColor: Cesium.Color.fromCssColorString("#fff"),
                horizontalOrigin: Cesium.VerticalOrigin.CENTER,
                verticalOrigin: Cesium.VerticalOrigin.BOTTOM,
                disableDepthTestDistance: Number.POSITIVE_INFINITY,
                showBackground: true,
                backgroundPadding: new Cesium.Cartesian2(10, 10),
            },
        });
        airline_entitys.push(label_entity);
    },

```

$("#to2d").click(function () {

  viewer.scene.morphTo2D(1);//二维

  });

  $("#to3d").click(function () {

    viewer.scene.morphTo3D(1);//三维

  });

  $("#toGLB").click(function () {

    viewer.scene.morphToColumbusView(1);//哥伦布视图

  });

