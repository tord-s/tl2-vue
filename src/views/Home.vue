<template>
  <main>
    <div class="bg-gray-50">
      <div
          class="max-w-screen-xl px-4 py-12 mx-auto sm:px-6 lg:py-16 lg:px-8 lg:flex lg:items-center lg:justify-between">

        <ul class="list" v-if="evauations">
          <li class="inline-block m-10 hover:bg-gray-300 bg-gray-200 bg-gray-100 border-6 px-10 py-5" v-for="evaluation in evauations" :key="evaluation._id">
            
              <h2 class="text-2xl font-bold text-blue-800 mb-4">{{ evaluation.title }} </h2>
              <!-- <img class="shadow-lg" v-if="album.cover" :src="imageUrlFor(album.cover).ignoreImageParams().width(240)"/> -->
              <h2 class="text-xl font-bold text-gray-700 mt-2 ">{{ evaluation.description }} </h2>
              <h2 class="font-medium text-gray-600"><em>{{ evaluation.apiUrl }}</em></h2>
          </li>
        </ul>
      </div>
    </div>

  </main>
</template>

<script>
import {onMounted, ref} from 'vue';

// sanity
import axios from 'axios';

export default {
  name: "Evaluation",
   data() {
    return {
      evals: [],
    }
  },
  setup() {
    onMounted(() => {
      fetchEvals();
    })

    let evauations = ref([])
    
    async function fetchEvals() {
      axios
      .get("https://l0in476p.api.sanity.io/v1/data/query/production?query=*%5B%20_type%3D%3D'evaluation'%5D%7B_id%2Ctitle%2CapiUrl%2Cdescription%7D")
      .then(response => {
        console.log(response.data.result)
        evauations.value = response.data.result;
        })
      }
       return {
        evauations
       }
    }

    // const groqQuery = `*[ _type=='evaluation']{_id,title,apiUrl,description}`;

    // let albums = ref([])

    // const imageUrlFor = (source) => {
    //   return imageBuilder.image(source);
    // }

    // function fetchAlbums() {
    //   sanityClient.fetch(groqQuery).then(
    //       albumResults => {
    //         albums.value = albumResults;
    //       },
    //       error => {
    //         this.error = error;
    //       }
    //   );
    // }

    // return {
    //   albums,
    //   imageUrlFor,

    // }
  // }
}
</script>