<template>
  <div>
    <div id="map"></div>
    <template v-if="!!this.google && !!this.map">
      <map-provider
        :google="google"
        :map="map"
      >
        <slot/>
      </map-provider>
    </template>
  </div>
</template>

<script>
import GoogleMapsApiLoader from 'google-maps-api-loader'
import MapProvider from './MapProvider'

export default {
  props:{
    mapConfig: Object,
    apiKey: String
  },
  components: {
    MapProvider
  },
  data(){
    return {
      google: null,
      map: null
    }
  },
  mounted () {
    GoogleMapsApiLoader({
      apiKey: this.apiKey
    }).then((google) => {
      this.google = google
      this.initializeMap()
    })
  },
  methods: {
    initializeMap (){
      const mapContainer = this.$el.querySelector('#map')
      const { Map } = this.google.maps
      this.map = new Map(mapContainer, this.mapConfig)


    var request = {
        origin: new google.maps.LatLng(35.690329, 139.700436), // 出発地
        destination: new google.maps.LatLng(35.692022, 139.770889), // 目的地
        waypoints: [ // 経由地点(指定なしでも可)
            { location: new google.maps.LatLng(35.658252, 139.701647) },
            { location: new google.maps.LatLng(35.666632, 139.758318) },

        ],
        travelMode: google.maps.DirectionsTravelMode.DRIVING, // 交通手段(歩行。DRIVINGの場合は車)
    }



    // マップの生成
    var map = new google.maps.Map(document.getElementById("map"), {
        center: new google.maps.LatLng(35.681382,139.766084), // マップの中心
        zoom: 10 // ズームレベル
    });

    // var trafficLayer = new google.maps.TrafficLayer();
    //     trafficLayer.setMap(map);
    var d = new google.maps.DirectionsService(); // ルート検索オブジェクト
    var r = new google.maps.DirectionsRenderer({ // ルート描画オブジェクト
        map: map, // 描画先の地図
        preserveViewport: false, // 描画後に中心点をずらさない
    })

 

        //marker.setMap(map)

      var flightPlanCoordinates = [
        {lat: 35.690329, lng: 139.700436},
        {lat: 35.658252, lng: 139.701647},
        {lat: 35.666632, lng: 139.758318},
        {lat: 35.692022, lng: 139.770889},
      ];
      var flightPath = new google.maps.Polyline({
        path: flightPlanCoordinates,
        geodesic: true,
        strokeColor: '#FF0000',
        strokeOpacity: 1.0,
        strokeWeight: 4
      });

        flightPath.setMap(map);
    // ルート検索
    d.route(request, function(result, status){
        // OKの場合ルート描画
        if (status == google.maps.DirectionsStatus.OK) {
            r.setDirections(result);
        }
    })
    }
  }
  
}

</script>

<style scoped>
#map {
  height: 100vh;
  width: 100%;
}
</style>
