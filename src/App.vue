<template>
  <div id="app">

    <div id="map"></div>
  </div>
</template>

<script>
import mapboxgl from 'mapbox-gl'
import 'mapbox-gl/dist/mapbox-gl.css'

const MAPBOX_TOKEN = 'pk.eyJ1Ijoic2hhbWlsMDgiLCJhIjoiY2txNjhnaTU1MTRqeTJ2bzZyYW5kZ2xnaiJ9.yji76OB2QSEe1cxKMcyjqA'


export default {
  name: 'App',
  data () {
    return {
      theme: {
        light: 'shamil08/ckqlzjd685shk18pela1yz2ap',
        dark: 'shamil08/ckqlzpgb34s9s17pml90xi2uu'
      },
      projects: [
        {
          code: 'Профсоюзная ГП1 Тюмень',
          latitude: 55.7,
          longitude: 38,
          builds: [
            { code: 'Корпус 6Д', laborAll: 1231, laborDone: 123, laborVerified: 50 },
            { code: 'Корпус 5A', laborAll: 28.3, laborDone: 4.9, laborVerified: 4.9 },
            { code: 'Корпус 4Б', laborAll: 28.3, laborDone: 4.9, laborVerified: 4.9 },
          ]
        },
        {
          code: 'Проект ТОМ-НОВ',
          latitude: 56.4,
          longitude: 85,
          builds: [
            { code: 'Сборка 6Д', laborAll: 1231, laborDone: 123, laborVerified: 50 },
            { code: 'Корпус 5A', laborAll: 28.3, laborDone: 4.9, laborVerified: 4.9 },
            { code: 'Этаж 1', laborAll: 28.3, laborDone: 4.9, laborVerified: 4.9 },
          ]
        }
      ]
    }
  },
  mounted() {
    mapboxgl.accessToken = MAPBOX_TOKEN
    const map = new mapboxgl.Map({
      container: 'map',
      maxZoom: 18,
      minZoom: 3,
      style: `mapbox://styles/${this.theme.light}`,
      center: [68, 58],
      zoom: 4,
    })
    // Add zoom and rotation controls to the map.
    map.addControl(new mapboxgl.NavigationControl());

    map.addControl(new mapboxgl.FullscreenControl());

    // Add geolocate control to the map.
    map.addControl(
        new mapboxgl.GeolocateControl({
          positionOptions: {
            enableHighAccuracy: true
          },
          trackUserLocation: true
        })
    );

    /** Data and popup content */
    map.on('load', () => {
      for (const p of this.projects) {
        new mapboxgl.Popup({ closeOnClick: false })
            .setLngLat([p.longitude, p.latitude])
            .setHTML(this.createProjectContent(p))
            .addTo(map);
      }
    })
  },
  methods: {
    createProjectContent (p) {
      const contBuilds = p.builds.reduce((acc, b) => {
        const name = `<p class="progress-code"> ${b.code} </p>`

        return acc + name + this.progressContent(b)
      }, '')

      return `<h3 class="project-title">${p.code}</h3> ${contBuilds}`
    },
    progressContent (b) {
      const progressDone = b.laborDone * 100 / b.laborAll
      const progressVerified = b.laborVerified * 100 / b.laborAll
      return `<div class="progress-container">
                <div class="progress" style="width: ${progressDone + progressVerified}%"></div>
                <div class="progress progress-verified" style="width: ${progressVerified}%"></div>
                <div class="progress-labors"><span class="all">${b.laborAll}</span> / <span class="done">${b.laborDone}</span> / <span class="verified">${b.laborVerified}</span> тр.д.</div>
              </div>`
    }
  }
}
</script>

<style>
body { margin: 0; padding: 0; }
#map { position: absolute; top: 0; bottom: 0; width: 100%; }

/* mapbox popup styles */
.mapboxgl-popup {
  width: 400px;
}
.mapboxgl-popup-close-button {
  display: none;
}
.mapboxgl-popup-content {
  padding-bottom: 20px;
}

.project-title {
  margin-top: 0;
  margin-bottom: 15px;
}

/* Progress bar style */
.progress-code {
  margin-top: 10px;
  margin-bottom: 2px;
}
.progress-container {
  height: 15px;
  width: 220px;
  border-radius: 0.4rem;

  background: #f0f0f0;
}
.progress-labors {
  text-align: right;
}
.progress-labors .done {
  color: #fbbd08;
}
.progress-labors .verified {
  color: #21ba45;
}

.progress-container .progress {
  height: 100%;
  width: 0;
  border-radius: 0.4rem;

  background: #fbbd08;

  transition: width 0.4s ease;

  text-align: right;
  font-size: 12px;
}
.progress-container .progress span {
  margin-right: 3px;
}
.progress-container div.progress-verified {
  background: #21ba45;
  margin-top: -15px;
}
</style>
