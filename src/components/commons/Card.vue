<template>
  <div
    class="card p-4 w-full max-w-md bg-white rounded-lg border shadow-md dark:bg-gray-800 dark:border-gray-700"
  >
    <h2 class="font-semibold mb-2 text-sm xl:text-lg">{{ item.tanggal }}</h2>
    <hr>
    <div class="flow-root overflow-y-auto text-xs xl:text-base mt-1">
      <ul role="list" class="divide-y divide-gray-200 dark:divide-gray-700">
        <li v-for="(detail, i) in item.detail" :key="i" class="py-1 mx-1">
          <div class="grid grid-cols-3 items-center gap-x-2">
            <div class="flex min-w-0 col-span-2">
              <p class="mr-2 font-medium text-gray-900 dark:text-white">
                {{ detail.jam }}
              </p>
              <p
                class="font-medium text-gray-900 dark:text-white break-all"
              >
                {{ detail.nama }}
              </p>
            </div>

            <div
              class="inline-flex items-center justify-end text-gray-900 dark:text-white"
            >
              {{ formatRupiah(detail.pengeluaraan) }}
            </div>
          </div>
        </li>
      </ul>
    </div>
    <div class="flex justify-end font-semibold border-t-2 border-gray-300 pt-2 mt-2 text-sm xl:text-lg">
      <span class="mr-6">Total</span>
      <span>{{ getTotal || 0 }}</span>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import rupiahFormater from "@/helpers/rupiahFormater";

export default defineComponent({
  name: "CardComponent",
  mixins: [rupiahFormater],
  props: {
    item: {
      type: Object,
      require: true,
    },
  },
  computed: {
    getTotal() {
      let sum = 0;
      // fungsi untuk mengambil data total pengeluaran
      for (let index = 0; index < this.item?.detail.length; index++) {
        sum += this.item?.detail[index].pengeluaraan;
      }

      return this.formatRupiah(sum);
    },
  },
});
</script>

<style scoped>
/* style scroll jika data overflow */
.flow-root{
  max-height: 220px;
}
.flow-root::-webkit-scrollbar {
  width: 0.3em;
  border-radius: 18px;
}
.flow-root::-webkit-scrollbar-track {
  opacity: 0.32;
  border-radius: 18px;
  background-color: rgba(238, 238, 238, 0.438);
}

.flow-root::-webkit-scrollbar-thumb {
  background-color: #b3b7c6;
  border-radius: 18px;
}
</style>

