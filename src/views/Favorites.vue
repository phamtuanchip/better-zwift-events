<template>
  <v-container>
    <div v-if="loaded">
      <div id="events" v-if="events && events.length">
        <div id="event" class="mb-4" v-for="event in events" :key="event.id">
          <Event
            :event="event"
            :now="now"
            :fav="isFav(event.id)"
            @favorited="removeFromFav"
            data-aos="fade"
            data-aos-easing="ease-in"
            data-aos-once="true"
          />
        </div>
      </div>
      <div v-else id="none" class="d-flex justify-center align-center">
        <h3>
          <span>No favorites</span>
        </h3>
      </div>
    </div>
    <div v-else id="loader" class="d-flex justify-center align-center">
      <MessageEllipsis message="Loading" />
    </div>
  </v-container>
</template>

<script>
import { mapGetters } from "vuex";
import Event from "@/components/Event.vue";
import MessageEllipsis from "@/components/MessageEllipsis.vue";

export default {
  name: "FavoritesView",
  data: () => ({
    now: null,
    events: null,
    loaded: false
  }),
  async created() {
    this.now = new Date();
    const favorites = await this.$dexie.getAll();

    this.loaded = true;
    this.events = favorites || [];
  },
  computed: {
    ...mapGetters({ isFav: "isFavorite" })
  },
  methods: {
    async removeFromFav({ id, value }) {
      await this.$store.dispatch("setAsFavorite", { id, value });
      const index = this.events.findIndex(event => event.id === id);
      if (index > -1) {
        this.events.splice(index, 1);
      }
    }
  },
  components: {
    Event,
    MessageEllipsis
  }
};
</script>

<style scoped>
#loader,
#none {
  height: 80vh;
}
</style>
