<template>
  <div id="homepage" class="container mx-auto my-6">
    <section class="section-title text-center">
      <h2 class="text-2xl font-semibold">Diari Jajan {{ getBulan }} 2022</h2>
      <p>Pengeluaraan Bulan ini {{ formatRupiah(totalPengeluaran) }}</p>
      <div class="flex justify-center">
        <Button
          class="bg-purple-700 my-4 hover:bg-purple-600"
          :params="true"
          @click="modalHandler(true)"
          >Tambah Item</Button
        >
      </div>
    </section>
    <section class="section-items">
      <div class="grid grid-cols-1 md:grid-cols-3 lg:grid-cols-4 gap-2 mx-4">
        <Card v-for="item in dataPengeluaran" :key="item.id" :item="item" />
      </div>
    </section>
    <Modal
      :isOpen="isOpen"
      @closeModal="modalHandler"
      @addItemsHandler="addItemsHandler"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { responseType } from "@/types/detailItemsType";
import rupiahFormater from "@/helpers/rupiahFormater";
import Button from "@/components/commons/Button.vue";
import Card from "@/components/commons/Card.vue";
import Modal from "@/components/commons/Modal.vue";

export default defineComponent({
  name: "HomePage",
  mixins: [rupiahFormater],
  components: {
    Button,
    Card,
    Modal,
  },
  data() {
    return {
      dataPengeluaran: [],
      totalPengeluaran: 0,
      isOpen: false,
    };
  },
  mounted() {
    this.refetch();
  },
  computed: {
    getBulan() {
      const today = new Date();
      let bulan: string | number = today.getMonth();
      switch (bulan) {
        case 0:
          bulan = "Januari";
          break;
        case 1:
          bulan = "Februari";
          break;
        case 2:
          bulan = "Maret";
          break;
        case 3:
          bulan = "April";
          break;
        case 4:
          bulan = "Mei";
          break;
        case 5:
          bulan = "Juni";
          break;
        case 6:
          bulan = "Juli";
          break;
        case 7:
          bulan = "Agustus";
          break;
        case 8:
          bulan = "September";
          break;
        case 9:
          bulan = "Oktober";
          break;
        case 10:
          bulan = "November";
          break;
        case 11:
          bulan = "Desember";
          break;
      }

      return bulan;
    },
  },
  methods: {
    async refetch() {
      try {
        const res = await fetch("http://localhost:3000/detail");
        if (!res.ok) {
          const message = `An error has occured: ${res.status} - ${res.statusText}`;
          throw new Error(message);
        }
        const data = await res.json();
        const result = {
          status: res.status + "-" + res.statusText,
          headers: {
            "Content-Type": res.headers.get("Content-Type"),
            "Content-Length": res.headers.get("Content-Length"),
          },
          length: res.headers.get("Content-Length"),
          data: data,
        };

        // Data dari result disusun ulang berdasarkan tanggal pembuatan item agar mempermudah proses maping
        const newResult = result.data
          // sorting tanggal dari yang terbaru
          .sort(function (a: responseType, b: responseType) {
            return b.tanggal.localeCompare(a.tanggal);
          })
          .reduce(
            (
              acc: responseType[],
              { tanggal, jam, nama, pengeluaraan }: responseType
            ): responseType[] => {
              // pada parameter acc dilakukan pencarian data yang memiliki tanggal yang sama.
              const found = acc.find((x: responseType) => x.tanggal === tanggal);
              // Bila data (found) exist maka data jam, nama, dan pengeluaran akan di push kedalam properti detail pada data terkait (found).
              if (found) {
                found["detail"]?.push({ jam, nama, pengeluaraan });
              } else {
                // Bila data (found) not exist maka sistem akan menambah object baru yang berisi tanggal dan detail pengeluaran.
                acc.push({
                  tanggal,
                  ["detail"]: [{ jam, nama, pengeluaraan }],
                });
              }
              return acc;
            },
            []
          );
        console.log(newResult);
        let sum = 0;
        // fungsi untuk mengambil data total pengeluaran
        for (let index = 0; index < result.data.length; index++) {
          sum += result.data[index].pengeluaraan;
        }

        this.totalPengeluaran = sum;
        this.dataPengeluaran = newResult;
      } catch (err) {
        console.log(err);
      }
    },
    modalHandler(value: boolean): void {
      this.isOpen = value;
    },
    async addItemsHandler({ nama, harga }: { nama: string; harga: number }) {
      // mengambil data tanggal hari ini
      const today = new Date();
      const tahun = today.getFullYear();
      const hari = today.getDate();

      const tanggal = hari + " " + this.getBulan + " " + tahun;
      const numPrefix = (num: number | string) => {
        let prefixed = num;
        if (num < 10) {
          prefixed = "0" + num;
        }

        return prefixed;
      };
      const postData = {
        jam: numPrefix(today.getHours()) + ":" + numPrefix(today.getMinutes()),
        nama,
        tanggal,
        pengeluaraan: harga,
      };
      try {
        const res = await fetch("http://localhost:3000/detail", {
          method: "post",
          headers: {
            Accept: "application/json",
            "Content-Type": "application/json",
          },
          body: JSON.stringify(postData),
        });
        if (!res.ok) {
          const message = `An error has occured: ${res.status} - ${res.statusText}`;
          throw new Error(message);
        }
        const data = await res.json();
        const result = {
          status: res.status + "-" + res.statusText,
          headers: {
            "Content-Type": res.headers.get("Content-Type"),
            "Content-Length": res.headers.get("Content-Length"),
          },
          data: data,
        };
        this.refetch();
        this.isOpen = false;
        console.log(result);
      } catch (err) {
        console.log(err);
      }
    },
  },
});
</script>

<style scoped></style>
