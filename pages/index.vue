<template>
  <div class="py-2 px-2 md:px-48 lg:px-60 xl:px-72">
    <div v-if="loading">Loading...</div>
    <div v-else>
      <div
        class="bg-cyan-700 rounded-lg overflow-hidden col-span-4 mb-4 md:max-h-[500px] relative grid place-content-center h-[28rem]"
      >
        <img
          class="absolute object-cover h-full w-full z-0 brightness-100 contrast-75 duration-300"
          :src="cities[0].img_url"
          alt="hero_image"
        />
        <div
          class="absolute inset-0 bg-[radial-gradient(169.40%_89.55%_at_94.76%_6.29%,rgba(0,0,0,0.40)_0%,rgba(255,255,255,0.00)_100%)] from-transparent to-black"
        ></div>

        <div
          class="z-10 flex flex-col duration-300 gap-y-5 justify-between text-center text-white px-4"
        >
          <p class="uppercase font-light text-sm">front end</p>
          <p class="text-6xl font-bold">valtech_</p>
          <p class="text-sm w-96 font-light">
            Lorem ipsum, dolor sit amet consectetur adipisicing elit. Ipsum quibusdam,
            quam atque et id ipsa corporis eum, iste excepturi quisquam voluptate.
          </p>
        </div>
      </div>
      <div class="grid grid-cols-1 gap-y-4 gap-x-4 md:grid-cols-4 grid-flow-dense">
        <div
          v-for="city in iterableCities"
          class="bg-cyan-700 rounded-lg overflow-hidden md:h-80 md:first:col-span-2 md:last:col-span-2 relative grid place-content-center duration-300 cursor-pointer group p-2"
        >
          <img
            :src="city.img_url || null"
            alt=""
            class="absolute object-cover h-full w-full z-0 brightness-75 contrast-75 hover:contrast-50 hover:brightness-50 duration-300"
          />
          <div
            class="z-10 text-white uppercase text-center pointer-events-none md:group-hover:-translate-y-32 lg:group-hover:-translate-y-28 duration-1000 absolute top-[50%] left-[50%] translate-y-[-50%] translate-x-[-50%]"
          >
            <p class="text-lg font-light">{{ city.country.replace("_", " ") }}</p>
            <p class="font-bold text-xl">{{ city.city.replace("_", " ") }}</p>
          </div>
          <div class="flex flex-col duration-300 gap-y-10 justify-between">
            <div
              class="opacity-0 md:group-hover:block md:group-hover:opacity-100 z-10 sm:text-[8px] lg:text-xs text-white text-center font-light pointer-events-none duration-1000 mt-10"
            >
              <p>
                {{ city.description }}
              </p>
            </div>
            <div
              class="opacity-0 md:group-hover:block md:group-hover:opacity-100 z-10 text-center pointer-events-none duration-1000"
            >
              <button
                class="rounded-sm md:rounded-md ring-1 ring-white text-white text-[8px] md:text-xs md:py-1 md:px-2 lg:py-2 lg:px-6 pointer-events-auto"
              >
                Explore More
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const cities = ref([
  {
    country: "bangladesh",
    city: "dhaka",
    description:
      "Dhaka, capital of Bangladesh. One of the most densed area in terms of habitation",
    img_url: null,
  },
  {
    country: "france",
    city: "paris",
    img_url: null,
    description:
      "Paris, capital of France. City of love and fashion. Nice place to visit with your loved one.",
  },
  {
    country: "germany",
    city: "berlin",
    img_url: null,
    description:
      "Berlin, a vibrant blend of history and modernity, pulsating with creative energy and cultural diversity.",
  },
  {
    country: "japan",
    city: "tokyo",
    img_url: null,
    description:
      "Tokyo, a futuristic metropolis where ancient traditions harmonize with cutting-edge technology and innovation.",
  },
  {
    country: "australia",
    city: "sidney",
    img_url: null,
    description:
      "Sydney, a stunning harbor city adorned with iconic landmarks, where sun-kissed beaches meet urban sophistication.",
  },
  {
    country: "india",
    city: "delhi",
    img_url: null,
    description:
      "Delhi, an ancient city rich in heritage, colors, and flavors, where tradition meets bustling cosmopolitan life.",
  },
  {
    country: "united_states",
    city: "new_york",
    img_url: null,
    description:
      "New York, the city that never sleeps, a melting pot of cultures, towering skyscrapers, and endless opportunities.",
  },
]);
const iterableCities = ref([]);
const runtimeConfig = useRuntimeConfig();
const apiKey = runtimeConfig.public.apiKey;
async function fetchCityImage(city, country) {
  const query = `${country}-${city}`.toLowerCase().replace(/\s/g, "-");
  const url = `https://api.pexels.com/v1/search?query=${query}&per_page=1`;

  try {
    const { data: imageInfo } = await useFetch(url, {
      headers: {
        Authorization: apiKey,
      },
      transform: (response) => {
        return response.photos[0].src.original;
      },
    });

    return imageInfo.value;
  } catch (error) {
    console.error(`Error fetching image for ${city}:`, error);
    return null;
  }
}

const loading = ref(false);

async function fetchAllCityImages() {
  loading.value = true;
  for (const city of cities.value) {
    const imageUrl = await fetchCityImage(city.name, city.country);
    if (imageUrl !== null) {
      city.img_url = imageUrl;
    }
  }
  iterableCities.value = cities.value.slice(1);
  console.log(iterableCities.value);
  loading.value = false;
}
fetchAllCityImages();
</script>

<style scoped></style>
