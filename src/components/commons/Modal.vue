<template>
  <div
    v-if="isOpen"
    class="fixed inset-0 flex items-center justify-center bg-gray-700 bg-opacity-50 h-auto"
  >
    <div class="max-w-2xl p-6 bg-white rounded-md shadow-xl">
      <div class="flex items-center justify-between mb-4">
        <h3 class="text-2xl">Tambah Entri</h3>
      </div>
      <hr />
      <div class="mt-4">
        <div class="w-full max-w-md">
          <form>
            <div class="mb-2">
              <label
                class="block text-gray-700 text-sm font-bold mb-2"
                for="nama"
              >
                Nama
              </label>
              <input
                :class="`shadow appearance-none ${
                  isRequire && inputValue.nama === '' ? 'border-red-500' : ''
                } border rounded w-full sm:w-72 py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline`"
                id="nama"
                v-model="inputValue.nama"
                type="text"
                placeholder="Masukan nama item"
              />
            </div>
            <div class="mb-4">
              <label
                class="block text-gray-700 text-sm font-bold mb-2"
                for="harga"
              >
                Harga
              </label>
              <input
                :class="`shadow appearance-none ${
                  isRequire && inputValue.harga === 0 ? 'border-red-500' : ''
                } border rounded w-full sm:w-72 py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline`"
                id="harga"
                v-model="inputValue.harga"
                type="number"
                min="0"
                placeholder="Masukan harga"
              />
            </div>
            <hr />
            <div class="flex justify-end mt-4">
              <Button class="bg-gray-500 mr-2" @click="closeModalHandler"
                >Batal</Button
              >
              <Button class="bg-purple-800" @click.prevent="addItemsHandler"
                >Kirim</Button
              >
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Button from "../commons/Button.vue";
export default defineComponent({
  name: "ModalComponent",
  components: {
    Button,
  },
  data() {
    return {
      inputValue: {
        nama: "" as string,
        harga: 0 as number,
      },
      isRequire: false as boolean,
    };
  },
  emits: ["closeModal", "addItemsHandler"],
  props: {
    isOpen: {
      type: Boolean,
      default: false,
    },
  },
  methods: {
    closeModalHandler() {
      this.$emit("closeModal", false);
      this.inputValue = {
        nama: "",
        harga: 0,
      }
      this.isRequire = false;
    },
    addItemsHandler() {
      // validasi jika input kosong
      const requiredValidation = Object.values(this.inputValue).includes(
        0 || ""
      );

      if (requiredValidation) {
        this.isRequire = true;
      } else {
        this.$emit("addItemsHandler", this.inputValue);
      }
    },
  },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
form {
  max-width: 300px;
}
</style>
