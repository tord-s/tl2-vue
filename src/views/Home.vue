<template>
  <main>
    <div class="bg-gray-50">
      <div
          class="max-w-screen-xl px-4 py-12 mx-auto sm:px-6 lg:py-16 lg:px-8 lg:flex lg:items-center lg:justify-between">

        <ul class="list" v-if="albums">
          <li class="inline-block m-10 hover:bg-gray-300 bg-gray-200 bg-gray-100 border-6 px-10 py-5" v-for="album in albums" :key="album._id">
            <router-link :to="{name: 'Album', params: {artist: album.artist, title: album.title}}">
              <h2 class="text-2xl font-bold text-blue-800 mb-4">{{ album.title }} </h2>
              <img class="shadow-lg" v-if="album.cover" :src="imageUrlFor(album.cover).ignoreImageParams().width(240)"/>
              <h2 class="text-xl font-bold text-gray-700 mt-2 ">{{ album.artist }} </h2>
              <h2 class="font-medium text-gray-600"><em>{{ album.releaseYear }}</em></h2>
            </router-link>
          </li>
        </ul>
      </div>
    </div>

  </main>
</template>

<script>
import {onMounted, ref} from 'vue';

// sanity
import sanityClient from "../../sanity-config";
import imageUrlBuilder from "@sanity/image-url";

const imageBuilder = imageUrlBuilder(sanityClient);

export default {
  name: "Albums",
  setup() {
    onMounted(() => {
      fetchAlbums();
    })
    const groqQuery = `*[ _type=='album']{
                    "artist":artist->name,
                    _id,
                    cover,
                    title,
                    releaseYear,
                    }`;

    let albums = ref([])

    const imageUrlFor = (source) => {
      return imageBuilder.image(source);
    }

    function fetchAlbums() {
      sanityClient.fetch(groqQuery).then(
          albumResults => {
            albums.value = albumResults;
          },
          error => {
            this.error = error;
          }
      );
    }

    return {
      albums,
      imageUrlFor,

    }
  }

}
</script>