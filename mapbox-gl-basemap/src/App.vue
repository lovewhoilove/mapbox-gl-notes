<template>
    <div id="app">
        <basemap />
    </div>
</template>

<script>
// eslint-disable-next-line
/* eslint-disable */
import Basemap from './components/Basemap';

export default {
    name: 'App',
    components: { Basemap },
};
</script>

<style>
html,
body,
#app {
    position: relative;
    width: 100%;
    height: 100%;
    margin: 0;
}
</style>
