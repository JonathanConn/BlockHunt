<template>
    <div>
        <div>
            <GmapMap
                :center="center"
                :zoom="14"
                style="width: 100%; height: 600px"
                :options="{
                    zoomControl: true,
                    mapTypeControl: false,
                    scaleControl: false,
                    streetViewControl: false,
                    rotateControl: false,
                    fullscreenControl: false,
                    disableDefaultUI: false
                }"
                >
                <GmapMarker
                    :key="index"
                    v-for="(m, index) in markers"
                    :position="m.position"
                    :clickable="true"
                    :draggable="true"
                    @click="center=m.position"
                />
            </GmapMap>
        </div>
        
        <BottomNavComponent/>
    </div>
</template>

<script>
import BottomNavComponent from './BottomNavComponent.vue'
import { getMapsData } from '@/utils/altura';

export default {
    name: "AddGoogleMap",
    components:{
        BottomNavComponent,
    },
    async created() {
        const res = await getMapsData('0x6c8553317be866f283a3a0de25f9b63cfa76bf66');
        // this.markers = [{position: {lat: 43.640234, lng: -79.3534457}}]
        // console.log(res);
        this.markers = res;
    },
    data() {
        return {
            center: {
                lat: 43.640234,
                lng: -79.3534457,
            },
            markers: [],
            locPlaces: [],
            existingPlace: null,
        };
    },

    mounted() {
        this.locateGeoLocation();
    },

    methods: {
        initMarker(loc) {
            this.existingPlace = loc;
        },
        addLocationMarker() {
            if (this.existingPlace) {
                const marker = {
                    lat: this.existingPlace.geometry.location.lat(),
                    lng: this.existingPlace.geometry.location.lng(),
                };
                this.locationMarkers.push({ position: marker });
                this.locPlaces.push(this.existingPlace);
                this.center = marker;
                this.existingPlace = null;
            }
        },
        locateGeoLocation: function () {
            navigator.geolocation.getCurrentPosition((res) => {
                this.center = {
                    lat: res.coords.latitude,
                    lng: res.coords.longitude,
                };
            });
        },
    },
};
</script>