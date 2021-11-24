<template>
  <main>
    <div class="max-w-screen-xl px-4 py-12 mx-auto sm:px-6 lg:py-16 lg:px-8 lg:inline-block lg:items-center lg:justify-between">
      <div v-if="album" class="inline-block">
        <h2 class="text-2xl font-bold text-blue-800 mb-2">{{ album.title }} </h2>
        <img class="shadow-lg" v-if="album.cover" :src="imageUrlFor(album.cover).ignoreImageParams().width(240)"/>
        <h2 class="text-2xl font-bold text-gray-700 mt-2 ">{{ album.artist }} </h2>
        <h2 class="font-bold text-2xl text-gray-600">{{ album.releaseYear }}</h2>
      </div>

      <div v-if="album" class="mt-8 clear">
        <h2 class="text-2xl text-gray-700 font-bold pb-2">Rate this album:</h2>
        <form name="rating" method="POST">
          <input type="hidden" name="form-name" value="rating"/>
          <input type="hidden" name="album" v-model="album._id">
          <div class="my-7">
            <div class="font-medium text-xl">Rating: <span class="text-red-700">*</span></div>
              <label for="1star" class="font-medium">1<input class="ml-2 mr-6" type="radio" name="stars" value="1" id="1star" required/></label>
              <label for="2stars" class="font-medium">2<input class="ml-2 mr-6" type="radio" name="stars" value="2" id="2stars" required/></label>
              <label for="3stars" class="font-medium">3<input class="ml-2 mr-6" type="radio" name="stars" value="3" id="3stars" required/></label>
              <label for="4stars" class="font-medium">4<input class="ml-2 mr-6" type="radio" name="stars" value="4" id="4stars" required/></label>
              <label for="5stars" class="font-medium">5<input class="ml-2 mr-6" type="radio" name="stars" value="5" id="5stars" required/></label>
          </div>

          <div>
            <label class="font-medium text-xl">Comments:
              <textarea class="block border-2 border-blue-300 my-3" name='comment' cols="70" rows="3"/>
            </label>
          </div>
          <div>
            <button class="bg-blue-300 px-3 py-1 my-3 text-xl font-bold lg:float-right" type="submit">Submit</button>
          </div>
        </form>
      </div>

      <div v-if="album.ratings" class="lg:clear-right">
        <div class="text-2xl font-bold">Ratings:</div>
        <ul class="inline-block">
          <li class="mt-2" v-for="rating in album.ratings" :key="album.ratings._id">
            <span class="w-24 inline-block mr-4"><span v-for="star in rating.stars" class="text-red-700 text-xl">&starf;</span></span><span class="text-gray-700"><em>"{{ rating.comment }}"</em></span>
          </li>
        </ul>
      </div>
    </div>
  </main>
</template>

<script>
import {onMounted, ref} from 'vue';

// sanity
import sanityClient from "../../sanity-config"
import imageUrlBuilder from "@sanity/image-url"
import {useRoute} from "vue-router"

const imageBuilder = imageUrlBuilder(sanityClient);


export default {
  name: "Album",

  setup() {
    onMounted(() => {
      fetchAlbum();
    })
    let album = ref([])

    const {
      params: {artist, title}
    } = useRoute()

    const groqAlbumQuery = `*[ _type=='album' && title == '${title}' && artist->name == '${artist}'] {
                    "artist":artist->name,
                    _id,
                    cover,
                    title,
                    releaseYear,
                    "ratings": *[ _type == "rating" && references(^._id) ]{ stars, comment, _id}
                    }[0]`;

    function fetchAlbum() {
      sanityClient.fetch(groqAlbumQuery).then(
          albumResults => {
            album.value = albumResults;
          },
          error => {
            this.error = error;
          }
      );
    }

    const imageUrlFor = (source) => {
      return imageBuilder.image(source);
    }

    return {
      album,
      imageUrlFor,
    }
  }

}
</script>