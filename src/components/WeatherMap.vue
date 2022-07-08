<template>
    <div id="map" class="map"></div>
</template>

<script>
import tt from '@tomtom-international/web-sdk-maps';
import { mapGetters } from 'vuex'

export default {
    data() {
        return {
            keyMap: 'Ac83qdhSuzFDTJtIDhJATK7JA5GGZfoc',
            map: null,
            coords: null,
            cloudSource: {
                type:'raster',
                tiles: [],
                tileSize: 256,
                minZoom: 0,
                maxZoom: 12,
                attribution: 'OpenWeatherMap.Org'
            },
            cloudLayer: {
                id: 'clouds_layer',
                type: 'raster',
                source: 'cloud_source',
                layout: {'visibility': 'visible'}
            }
        }
    },
    mounted() {
        this.getCoords()
    },
    computed: {
        ...mapGetters(['key'])
    },
    methods: {
        setMap() {
            this.map = tt.map({
                key: this.keyMap,
                container: 'map',
                center: this.coords,
                zoom: 12
            });
        },
        setCenter() {

        },
        getCoords(city='Москва') {
            fetch(`http://api.openweathermap.org/geo/1.0/direct?q=${city}&limit=1&appid=${this.key}`)
                .then(res => res.json())
                .then(json => this.coords = {
                    lat: json[0].lat,
                    lon: json[0].lon
                })
                .then(() => {
                    this.setMap()
                    this.setLayer()
                })
        },
        setLayer() {
            this.cloudSource.tiles.push(`https://tile.openweathermap.org/map/clouds_new/{z}/{x}/{y}.png?appid=${this.key}`)
            this.map.on('load', () => {
                this.map.addSource('cloud_source', this.cloudSource)
                this.map.addLayer(this.cloudLayer)
                this.setEventClick()
            })
        },
        setEventClick() {
            this.map.on('click', (e) => {
                this.$emit('click-city', e.lngLat)
            })
        }
    }
}
</script>

<style lang="scss">
#map {
    height: 100vh;
    width: 100vw;
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
}
</style>