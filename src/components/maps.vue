<template>
  <div class="App"></div>
</template>

<script>
import gmapsInit from '../utils/gmaps.js';

export default {
  name: 'App',
  csvData: {type: Array, default: []},
  async mounted() {
    try {
      const google = await gmapsInit();
      const geocoder = new google.maps.Geocoder();
      const map = new google.maps.Map(this.$el);

      geocoder.geocode({ address: 'United Kingdom' }, (results, status) => {
        if (status !== 'OK' || !results[0]) {
          throw new Error(status);
        }

        map.setCenter(results[0].geometry.location);
        map.fitBounds(results[0].geometry.viewport);
        
        var url =
        "https://gist.githubusercontent.com/dancsita/c38b7febb413a0fd04a2f075f107c04a/raw/1618a76bc355b40645f6c876bf275797ad4bb1f9/locations.csv";

        var request = new XMLHttpRequest();
        request.open("GET", url, false);
        request.send(null);
        var csvData = [];
        var jsonObject = request.responseText.split(/\r?\n|\r/);
        for (var i = 1; i < jsonObject.length; i++) {
          let objectcurrent = jsonObject[i].split(",");
          csvData.push({
            position: {
              lat: parseFloat(objectcurrent[1]),
              lng: parseFloat(objectcurrent[2]),
            }
          });
        }

      
      const markers = csvData.map(x => new google.maps.Marker({ ...x, map }));
      console.log(markers.length); //solving no-unused-vars error
      });

      
    } catch (error) {
      console.error(error);
    }
  },
};
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
}

.App {
  width: 100vw;
  height: 100vh;
}
</style>