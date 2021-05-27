<template>
  <div v-if="isLoading === false">
    <p class="text-4xl">{{ bookName }}, {{ currentChapter }}</p>
    <ul class="mt-6">
      <li
        class="text-left odd:bg-gray-100 p-2"
        v-for="(verse, i) in verses"
        :key="i"
      >
        <span class="font-semibold">{{ i + 1 }}.</span> {{ verse.text }}
      </li>
    </ul>
    <div class="flex justify-center space-x-5 mt-12">
      <button
        class="p-3 bg-green-500 text-white shadow-md disabled:opacity-60 focus:outline-none rounded active:bg-green-600"
        :disabled="(bookIndex === 0 && currentChapter <= 1) || isLoading"
        @click="fetchPrev()"
      >
        Anterior
      </button>
      <button
        class="p-3 bg-green-500 text-white shadow-md disabled:opacity-60 focus:outline-none rounded active:bg-green-600"
        :disabled="isLoading"
        @click="fetchNext()"
      >
        Próximo
      </button>
    </div>
  </div>
</template>

<script>
import axios from "axios";

const token = process.env.VUE_APP_API_KEY;

const api = axios.create({
  baseURL: "https://www.abibliadigital.com.br/api/",
  headers: {
    Authorization: `Bearer ${token}`,
  },
});

export default {
  name: "Bible",
  data() {
    return {
      books: [],
      bookIndex: 0,
      currentChapter: 1,
      bookName: "",
      verses: [],
      isLoading: true,
    };
  },
  props: {
    version: {
      type: String,
    },
  },
  watch: {
    version: {
      handler() {
        this.getData();
      },
    },
  },
  async mounted() {
    await this.fetchBooks();
    await this.getData();
  },
  methods: {
    async fetchBooks() {
      const res = await api.get("books");
      this.books = res.data;
    },

    async getData() {
      this.verses = [];

      const book = this.books[this.bookIndex];

      const res = await api
        .get(
          `verses/${this.version || "acf"}/${book.abbrev.pt}/${
            this.currentChapter
          }`
        )
        .catch((err) => {
          console.log(err);
        });

      this.isLoading = false;
      this.bookName = res.data.book.name;
      this.verses = res.data.verses;
    },

    fetchNext() {
      this.isLoading = true;

      let book = this.books[this.bookIndex];

      const isFinalChapter = this.currentChapter === book.chapters;

      this.currentChapter++;

      if (isFinalChapter) {
        this.bookIndex++;
        book = this.books[this.bookIndex];
        this.currentChapter = 1;
      }

      this.getData();
    },

    fetchPrev() {
      this.isLoading = true;
      this.currentChapter--;

      let book = this.books[this.bookIndex];

      if (this.currentChapter < 1) {
        this.bookIndex--;
        book = this.books[this.bookIndex];
        this.currentChapter = book.chapters;
      }

      this.getData();
    },
  },
};
</script>
