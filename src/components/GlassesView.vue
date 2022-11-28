<script setup>
import CollectionsMenu from './CollectionsMenu.vue';
import { watch, onMounted, onUnmounted, ref } from 'vue';

const datas = ref([]);
const baseUrl = ref('https://staging-api.bloobloom.com/user/v1/sales_channels/website/collections/');
const baseFilterUrl = ref('/glasses?sort[type]=collection_relations_position&sort[order]=asc&filters[lens_variant_prescriptions][]=fashion&filters[lens_variant_types][]=classic');
const collection = ref('spectacles-men');
const page = ref(12);
const number = ref('&page[number]=1');
const color = ref('&filters[glass_variant_frame_variant_colour_tag_configuration_names][]=coloured&filters[glass_variant_frame_variant_colour_tag_configuration_names][]=black');
const shape = ref('&filters[glass_variant_frame_variant_frame_tag_configuration_names][]=round')
const variant = ref('&filters[frame_variant_home_trial_available]=false');
const fetchUrl = ref(baseUrl.value+ collection.value + baseFilterUrl.value + '&page[limit]=' + page.value + number.value + color.value + shape.value + variant.value);
const colorFilter = ref();
const shapeFilter = ref();
const colors = ref(['black', 'tortoise', 'coloured', 'crystal', 'dark', 'bright']);
const shapes = ref(['square', 'rectangle', 'round', 'cat-eye']);
const scrollY = ref(null);
const totalNumbers = ref(0)

async function fetchData() {
  const response = await fetch(fetchUrl.value);
  if (!response.ok) {
    throw new Error(`An error occured: ${response.status}`);
  }
  const fetchData = await response.json();
  totalNumbers.value = fetchData.meta.total_count;
  var arr = fetchData.glasses.map((glass) => {
    const colourUrl = glass.glass_variants[0].frame_variant.colour.media[0].url;
    const url = glass.glass_variants[0].media[0].url;
    const name = glass.name;
    return { colourUrl, url, name };
  })

  datas.value = arr;
}

function createUrl() {
  fetchUrl.value = baseUrl.value+ collection.value + baseFilterUrl.value + '&page[limit]=' + page.value + number.value + color.value + shape.value + variant.value;
}
watch(fetchUrl, fetchData);

onMounted(() => {
  fetchData();
  window.addEventListener("scroll", handleScroll)
})
onUnmounted(() => {
  window.removeEventListener("scroll", handleScroll)
})
const handleScroll = (e) => {
  let element = scrollY.value
  if (element.getBoundingClientRect().bottom < window.innerHeight) {
    if (page.value < totalNumbers.value) {
      page.value += 6;
    }
    createUrl();
  }
}

function filter(key) {
  if (key == 'color') {
    colorFilter.value = !colorFilter.value;
    shapeFilter.value = false;
  } else {
    shapeFilter.value = !shapeFilter.value;
    colorFilter.value = false;
  }
}
 
function addFilter(filterKey) {
  let key = filterKey.key;
  let value = filterKey.value;
  if (key == 'color') {
    color.value = '&filters[glass_variant_frame_variant_colour_tag_configuration_names][]=coloured&filters[glass_variant_frame_variant_colour_tag_configuration_names][]=' + value;
    page.value = 12;
  } else if (key == 'shape') {
    shape.value = '&filters[glass_variant_frame_variant_frame_tag_configuration_names][]=' + value;
    page.value = 12;
  } else {
    collection.value = key + '-' + value;
  }
  createUrl();
}
</script>

<template>
  <header class="container">
    <div class="top-bar">
      <div class="menu">MENU
        <CollectionsMenu @collection = "addFilter"/>
      </div>
      <img src="../assets/logo.png" alt="">
    </div>
  </header>
  <div class="container filter-bar">
    <div class="sub-bar">
      <div></div>
      <div class="title">SPECTACLES WOMEN</div>
      <div>
        <div class="filter" @click="filter('color')">COLOUR</div>
        <div class="filter" @click="filter('shape')">SHAPE</div>
      </div>
    </div>
    <ul :class="{'color-filter': true, 'active': colorFilter }">
      <li v-for="clr in colors" @click="addFilter({key:'color', value: clr})">{{ clr }}</li>
    </ul>
    <ul :class="{'color-filter': true, 'active': shapeFilter}">
      <li v-for="shp in shapes" @click="addFilter({key:'shape', value: shp})">{{ shp }}</li>
    </ul>
  </div>
  <div class="container view" ref="scrollY">
    <div class="img" v-for="data in datas">
      <img :src="data.url" alt="" />
      <div class="name">{{ data.name }}</div>
    </div>
  </div>

</template>

<style>

</style>