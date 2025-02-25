<template>
  <v-lazy>
    <v-hover v-slot="{ hover }" :open-delay="50">
      <v-card
        :class="{ 'on-hover': hover }"
        :elevation="hover ? 12 : 2"
        :to="route ? `/recipe/${slug}` : ''"
        :min-height="imageHeight + 75"
        @click="$emit('click')"
      >
        <RecipeCardImage
          :icon-size="imageHeight"
          :height="imageHeight"
          :slug="slug"
          :recipe-id="recipeId"
          small
          :image-version="image"
        >
          <v-expand-transition v-if="description">
            <div v-if="hover" class="d-flex transition-fast-in-fast-out secondary v-card--reveal" style="height: 100%">
              <v-card-text class="v-card--text-show white--text">
                {{ description }}
              </v-card-text>
            </div>
          </v-expand-transition>
        </RecipeCardImage>
        <v-card-title class="my-n3 px-2 mb-n6">
          <div class="headerClass">
            {{ name }}
          </div>
        </v-card-title>

        <slot name="actions">
          <v-card-actions class="px-1">
            <RecipeFavoriteBadge v-if="loggedIn" class="absolute" :slug="slug" show-always />

            <RecipeRating class="pb-1" :value="rating" :name="name" :slug="slug" :small="true" />
            <v-spacer></v-spacer>
            <RecipeChips :truncate="true" :items="tags" :title="false" :limit="2" :small="true" url-prefix="tags" />
            <RecipeContextMenu
              color="grey darken-2"
              :slug="slug"
              :name="name"
              :recipe-id="recipeId"
              :use-items="{
                delete: false,
                edit: true,
                download: true,
                mealplanner: true,
                shoppingList: true,
                print: false,
                share: true,
              }"
              @delete="$emit('delete', slug)"
            />
          </v-card-actions>
        </slot>
        <slot></slot>
      </v-card>
    </v-hover>
  </v-lazy>
</template>

<script lang="ts">
import { computed, defineComponent, useContext } from "@nuxtjs/composition-api";
import RecipeFavoriteBadge from "./RecipeFavoriteBadge.vue";
import RecipeChips from "./RecipeChips.vue";
import RecipeContextMenu from "./RecipeContextMenu.vue";
import RecipeCardImage from "./RecipeCardImage.vue";
import RecipeRating from "./RecipeRating.vue";

export default defineComponent({
  components: { RecipeFavoriteBadge, RecipeChips, RecipeContextMenu, RecipeRating, RecipeCardImage },
  props: {
    name: {
      type: String,
      required: true,
    },
    slug: {
      type: String,
      required: true,
    },
    description: {
      type: String,
      default: null,
    },
    rating: {
      type: Number,
      required: false,
      default: 0,
    },
    image: {
      type: String,
      required: false,
      default: "abc123",
    },
    route: {
      type: Boolean,
      default: true,
    },
    tags: {
      type: Array,
      default: () => [],
    },
    recipeId: {
      required: true,
      type: String,
    },
    imageHeight: {
      type: Number,
      default: 200,
    },
  },
  setup() {
    const { $auth } = useContext();
    const loggedIn = computed(() => {
      return $auth.loggedIn;
    });

    return {
      loggedIn,
    };
  },
});
</script>

<style>
.v-card--reveal {
  align-items: center;
  bottom: 0;
  justify-content: center;
  opacity: 0.8;
  position: absolute;
  width: 100%;
}
.v-card--text-show {
  opacity: 1 !important;
}
.headerClass {
  white-space: nowrap;
  word-break: normal;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>
