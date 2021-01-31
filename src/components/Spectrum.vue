<template>
  <!-- <v-skeleton-loader
    :loading="loading"
    :style="`width: 100%; height: ${height + 10}px; overflow: hidden;`"
    type="heading"
    class="loader--fullwidth"
    transition="fade-transition"
  > -->
    <div :key="0" style="max-height: 100%;">
      <v-sheet
        v-for="(item, i) in items"
        :key="i"
        :style="
          `display: inline-block; width: ${calculatePerc(item)}%;
          background-color: ${item.color};
           max-height: 100%;
           white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;`
        "
        :class="item.class"
        class="text-center"
        :dark="!item.light"
      >
        <span v-if="item.value">{{ item.value }}{{ item.suffix }}</span>
      </v-sheet>
    </div>
  <!-- </v-skeleton-loader> -->
</template>

<script lang="ts">
import { Vue, Component, Prop } from "vue-property-decorator";
interface Item {
  value: number;
  label: string;
  color: string;
  class: string;
  light: boolean;
  suffix?: string;
}
@Component
export default class Spectrum extends Vue {
  @Prop(Boolean)
  loading!: boolean;
  @Prop({ type: Array, required: true })
  items!: Item[];
  @Prop({ type: Number, default: "32" })
  height!: number;
  get total() {
    return this.items.reduce((t, c) => t + c.value, 0);
  }
  calculatePerc(item: Item) {
    return (Math.max(item.value, 0) / this.total) * 100;
  }
}
</script>

<style scoped lang="scss"></style>