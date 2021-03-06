<template>
  <div :class="`swiper-section-${uuid}`" style="width: 100%">
    <skeleton-home-section v-if="loading" :card-shape="shape" />
    <v-col v-show="items && items.length > 0" class="swiper-section">
      <div class="d-flex ma-2">
        <h1
          class="text-h6 text-sm-h5 font-weight-light header"
          :class="{ 'header-white-mode': !$vuetify.theme.dark }"
        >
          <span class="pl-4">{{ title }}</span>
        </h1>
        <v-spacer />
        <v-btn class="swiper-prev" icon>
          <v-icon>mdi-arrow-left</v-icon>
        </v-btn>
        <v-btn class="swiper-next mr-2" icon>
          <v-icon>mdi-arrow-right</v-icon>
        </v-btn>
      </div>

      <swiper class="swiper" :options="swiperOptions">
        <swiper-slide v-for="item in items" :key="item.Id">
          <card :shape="shape" :item="item" margin text overlay link />
        </swiper-slide>
      </swiper>
    </v-col>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import { SwiperOptions } from 'swiper';
import { v4 as uuidv4 } from 'uuid';
import { BaseItemDto } from '@jellyfin/client-axios';
import { CardShapes, getShapeFromItemType } from '~/utils/items';

export default Vue.extend({
  props: {
    loading: {
      type: Boolean,
      default(): boolean {
        return false;
      }
    },
    title: {
      type: String,
      required: true
    },
    items: {
      type: Array as () => BaseItemDto[],
      default(): BaseItemDto[] {
        return [];
      }
    },
    shape: {
      type: String,
      default(): string {
        return getShapeFromItemType(this.items?.[0]?.Type);
      }
    }
  },
  data() {
    const uuid = uuidv4();

    return {
      uuid,
      swiperOptions: {
        initialSlide: 0,
        freeMode: this.$browser.isMobile(),
        effect: 'slide',
        navigation: {
          nextEl: `.swiper-section-${uuid} .swiper-next`,
          prevEl: `.swiper-section-${uuid} .swiper-prev`
        },
        slidesPerView: this.shape === CardShapes.Thumb ? 2 : 3,
        slidesPerGroup: this.shape === CardShapes.Thumb ? 2 : 3,
        breakpoints: {
          600: {
            slidesPerView: this.shape === CardShapes.Thumb ? 3 : 4,
            slidesPerGroup: this.shape === CardShapes.Thumb ? 3 : 4
          },
          960: {
            slidesPerView: this.shape === CardShapes.Thumb ? 3 : 6,
            slidesPerGroup: this.shape === CardShapes.Thumb ? 3 : 6
          },
          1904: {
            slidesPerView: this.shape === CardShapes.Thumb ? 4 : 8,
            slidesPerGroup: this.shape === CardShapes.Thumb ? 4 : 8
          }
        }
      } as SwiperOptions
    };
  }
});
</script>

<style lang="scss" scoped>
@import '~vuetify/src/styles/styles.sass';

.swiper-section .header::before {
  background-color: #{map-get($material-dark, 'text-color')};
  content: '';
  position: relative;
  display: inline-block;
  height: 1px;
  bottom: 0.3em;
  left: 0;
  width: 1.25em;
}

.swiper-section .header-white-mode::before {
  background-color: #{map-get($material-light, 'text-color')};
}
</style>
