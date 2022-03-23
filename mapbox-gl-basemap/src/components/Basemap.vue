<template>
    <div id="map"></div>
</template>

<script>
// eslint-disable-next-line
/* eslint-disable */
import 'mapbox-gl/dist/mapbox-gl.css';
import mapboxgl from '!mapbox-gl';

export default {
    name: 'Basemap',
    mounted: function () {
        this._createBasemap(); //创建底图
    },
    methods: {
        /**
         * 创建底图
         */
        _createBasemap() {
            mapboxgl.accessToken =
                'pk.eyJ1IjoibG92ZXdob2lsb3ZlIiwiYSI6ImNreTl4c2p5cDAwNHQyb21yc3hzOTRhaXAifQ.IhEFeUXJ4WYpiAf-w7Vzsg';

            const map = new mapboxgl.Map({
                container: 'map',
                center: [121.500378, 24.95931], // starting position [lng, lat]
                zoom: 13, // starting zoom
                style: {
                    glyphs: 'mapbox://fonts/mapbox/{fontstack}/{range}.pbf',
                    version: 8,
                    sources: {
                        twSource: {
                            type: 'vector',
                            scheme: 'tms',
                            tiles: [
                                'http://localhost:8080/geoserver/gwc/service/tms/1.0.0/tw:tw@EPSG:900913@pbf/{z}/{x}/{y}.pbf',
                            ],
                        },
                    },
                    layers: [],
                },
            });

            map.on('load', function () {
                // --------- 背景 -----------
                map.addSource('bg', {
                    type: 'geojson',
                    data: {
                        type: 'FeatureCollection',
                        features: [
                            {
                                type: 'Feature',
                                properties: {},
                                geometry: {
                                    type: 'Polygon',
                                    coordinates: [
                                        [
                                            [-180, 90],
                                            [-180, -90],
                                            [180, -90],
                                            [180, 90],
                                            [-180, 90],
                                        ],
                                    ],
                                },
                            },
                        ],
                    },
                });

                // ------------------ bg
                map.addLayer({
                    id: 'bg',
                    source: 'bg',
                    type: 'fill',
                    paint: {
                        'fill-color': '#f8f4f0',
                    },
                });

                // ---------------- natural
                map.addLayer({
                    id: 'natural-not-water',
                    source: 'twSource',
                    'source-layer': 'natural',
                    type: 'fill',
                    //minzoom: 12,
                    filter: ['==', 'fclass', 'tree'],
                    paint: {
                        'fill-color': '#B3E49B',
                        'fill-opacity': 1,
                    },
                });

                map.addLayer({
                    id: 'natural-water',
                    source: 'twSource',
                    'source-layer': 'natural',
                    type: 'fill',
                    minzoom: 12,
                    filter: ['==', 'fclass', 'beach'],
                    paint: {
                        'fill-color': '#f3f0de',
                        'fill-opacity': 1,
                    },
                });

                // ---------------- landuse
                map.addLayer({
                    id: 'landuse',
                    source: 'twSource',
                    'source-layer': 'landuse',
                    type: 'fill',
                    // minzoom: 12,
                    paint: {
                        'fill-color': '#B3E49B',
                        'fill-opacity': 1,
                    },
                });

                // map.addLayer({
                //     id: 'landuse-residential',
                //     source: 'twSource',
                //     'source-layer': 'landuse',
                //     type: 'fill',
                //     filter: ['all', ['in', 'fclass', 'residential', 'suburb', 'neighbourhood']],
                //     // minzoom: 12,
                //     paint: {
                //         'fill-color': {
                //             base: 1,
                //             stops: [
                //                 [12, 'hsla(30, 19%, 90%, 0.4)'],
                //                 [16, 'hsla(30, 19%, 90%, 0.2)'],
                //             ],
                //         },
                //     },
                // });

                // map.addLayer({
                //     id: 'landuse-industrial',
                //     source: 'twSource',
                //     'source-layer': 'landuse',
                //     type: 'fill',
                //     filter: ['all', ['==', '$type', 'Polygon'], ['in', 'fclass', 'industrial', 'garages', 'dam']],
                //     // minzoom: 12,
                //     paint: { 'fill-color': 'hsla(49, 100%, 88%, 0.34)' },
                // });

                // map.addLayer({
                //     id: 'landuse-commercial',
                //     source: 'twSource',
                //     'source-layer': 'landuse',
                //     type: 'fill',
                //     filter: ['all', ['==', '$type', 'Polygon'], ['==', 'fclass', 'commercial']],
                //     // minzoom: 12,
                //     paint: { 'fill-color': 'hsla(0, 60%, 87%, 0.23)' },
                // });

                // map.addLayer({
                //     id: 'landuse-cemetery',
                //     source: 'twSource',
                //     'source-layer': 'landuse',
                //     type: 'fill',
                //     filter: ['==', 'fclass', 'cemetery'],
                //     paint: { 'fill-color': '#e0e4dd' },
                // });

                // map.addLayer({
                //     id: 'landuse-hospital',
                //     source: 'twSource',
                //     'source-layer': 'landuse',
                //     type: 'fill',
                //     filter: ['==', 'fclass', 'hospital'],
                //     paint: { 'fill-color': '#fde' },
                // });

                // map.addLayer({
                //     id: 'landuse-school',
                //     source: 'twSource',
                //     'source-layer': 'landuse',
                //     type: 'fill',
                //     filter: ['==', 'fclass', 'school'],
                //     paint: { 'fill-color': '#f0e8f8' },
                // });

                // map.addLayer({
                //     id: 'landuse-railway',
                //     source: 'twSource',
                //     'source-layer': 'landuse',
                //     type: 'fill',
                //     filter: ['==', 'class', 'railway'],
                //     paint: { 'fill-color': 'hsla(30, 19%, 90%, 0.4)' },
                // });

                //  --------------- roads
                map.addLayer({
                    id: 'roads-primary',
                    source: 'twSource',
                    'source-layer': 'roads',
                    filter: ['==', 'fclass', 'primary'],
                    type: 'line',
                    //   minzoom: 12,
                    paint: {
                        'line-color': '#ffffff',
                        'line-opacity': 1,
                        'line-width': 5,
                    },
                });

                map.addLayer({
                    id: 'roads-tertiary',
                    source: 'twSource',
                    'source-layer': 'roads',
                    filter: ['==', 'fclass', 'tertiary'],
                    type: 'line',
                    //   minzoom: 12,
                    paint: {
                        'line-color': '#ffffff',
                        'line-opacity': 1,
                        'line-width': 3,
                    },
                });

                // -------------------- railways
                map.addLayer({
                    id: 'railways',
                    source: 'twSource',
                    'source-layer': 'railways',
                    type: 'line',
                    //   minzoom: 12,
                    paint: {
                        'line-color': '#f2934a',
                        'line-opacity': 1,
                        'line-width': 3,
                    },
                });

                //------------------ building
                map.addLayer({
                    id: 'buildings',
                    source: 'twSource',
                    'source-layer': 'buildings',
                    type: 'fill',
                    minzoom: 13,
                    paint: {
                        'fill-color': '#dfdcd7',
                        'fill-opacity': 1,
                    },
                });

                map.addLayer({
                    id: 'buildings-stroke',
                    source: 'twSource',
                    'source-layer': 'buildings',
                    type: 'line',
                    minzoom: 18,
                    paint: {
                        'line-color': '#aaaaaa',
                        'line-opacity': 0.8,
                    },
                });

                // map.addLayer({
                //     id: '3d-buildings',
                //     source: 'twSource',
                //     'source-layer': 'buildings',
                //     type: 'fill-extrusion',
                //     minzoom: 12,
                //     paint: {
                //         'fill-extrusion-color': '#dfdcd7',
                //         'fill-extrusion-opacity': 1,
                //         'fill-extrusion-height': 50,
                //     },
                // });

                //------------------ waterways
                map.addLayer({
                    id: 'waterways',
                    source: 'twSource',
                    'source-layer': 'waterways',
                    type: 'line',
                    layout: { 'line-cap': 'round' },
                    paint: {
                        'line-color': '#75CFEF',
                        'line-dasharray': [2, 4],
                        'line-width': 3,
                    },
                });

                //----------------------- 注记
                //-----------------------poi
                map.addLayer({
                    id: 'points-name',
                    source: 'twSource',
                    'source-layer': 'pois',
                    type: 'symbol',
                    layout: {
                        'text-field': '{name}',
                        'text-transform': 'uppercase',
                        'text-font': ['Open Sans Regular', 'Arial Unicode MS Regular'],
                        'text-padding': 10,
                        'text-size': 12,
                    },
                    paint: {
                        'text-halo-color': 'rgb(200, 200, 200)',
                        'text-halo-width': 1,
                        'text-color': 'rgb(0, 0, 0)',
                        'text-halo-blur': 0.5,
                    },
                    interactive: true,
                });

                //-----------------------roads
                map.addLayer({
                    id: 'roads-name',
                    source: 'twSource',
                    'source-layer': 'roads',
                    type: 'symbol',
                    layout: {
                        'text-field': '{name}',
                        'text-transform': 'uppercase',
                        'text-font': ['Open Sans Regular', 'Arial Unicode MS Regular'],
                        'text-padding': 5,
                        'text-keep-upright': false,
                        'text-rotation-alignment': 'map',
                        'symbol-placement': 'line-center',
                        'text-pitch-alignment': 'viewport',
                        'text-size': 12,
                    },
                    paint: {
                        'text-halo-color': 'rgb(200, 200, 200)',
                        'text-halo-width': 1,
                        'text-color': 'rgb(0, 0, 0)',
                        'text-halo-blur': 0.5,
                    },
                    interactive: true,
                });

                //----------------------- railways
                map.addLayer({
                    id: 'railways-name',
                    source: 'twSource',
                    'source-layer': 'railways',
                    //   minzoom: 12,
                    type: 'symbol',
                    layout: {
                        'text-field': '{name}',
                        'text-transform': 'uppercase',
                        'text-font': ['Open Sans Regular', 'Arial Unicode MS Regular'],
                        'text-padding': 5,
                        'text-keep-upright': false,
                        'text-rotation-alignment': 'map',
                        'symbol-placement': 'line-center',
                        'text-pitch-alignment': 'viewport',
                        'text-size': 12,
                    },
                    paint: {
                        'text-halo-color': 'rgb(255, 255, 255)',
                        'text-halo-width': 1,
                        'text-color': 'rgb(0, 0, 200)',
                        'text-halo-blur': 0.5,
                    },
                    interactive: true,
                });

                // ------------------ waterways
                map.addLayer({
                    id: 'waterways-name',
                    source: 'twSource',
                    'source-layer': 'waterways',
                    //   minzoom: 12,
                    type: 'symbol',
                    layout: {
                        'text-field': '{name}',
                        'text-transform': 'uppercase',
                        'text-font': ['Open Sans Regular', 'Arial Unicode MS Regular'],
                        'text-padding': 5,
                        'text-keep-upright': false,
                        'text-rotation-alignment': 'map',
                        'symbol-placement': 'line-center',
                        'text-pitch-alignment': 'viewport',
                        'text-size': 12,
                    },
                    paint: {
                        'text-halo-color': 'rgb(255, 255, 255)',
                        'text-halo-width': 1,
                        'text-color': '#75CFEF',
                        'text-halo-blur': 0.5,
                    },
                    interactive: true,
                });
            });
        },
    },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#map {
    width: 100%;
    height: 100%;
    margin: 0;
}
</style>
