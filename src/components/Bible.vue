<template>
  <div class="flex flex-col justify-center items-center">
    <div class="flex flex-col lg:flex-row lg:space-x-10">
      <div
        class="flex flex-col md:flex-row items-center justify-center space-x-2"
      >
        <span class="text-lg">Bíblias:</span>
        <select
          v-model="version"
          class="p-1 border-2 border-gray-200 focus:outline-none"
        >
          <option value="" selected disabled>Selecione uma versão</option>
          <option value="acf">Almeida Corrigida Fiel</option>
          <option value="nvi">Nova Versão Internacional</option>
        </select>
      </div>

      <div
        class="flex flex-col md:flex-row items-center justify-center space-x-2 mt-2 md:mt-6 lg:mt-0"
      >
        <span class="text-lg">Livros:</span>
        <select
          v-model="newBook"
          class="p-1 border-2 border-gray-200 focus:outline-none"
          @change="handleBookChange()"
        >
          <option value="" selected disabled>Selecione um livro</option>
          <option v-for="(book, i) in books" :key="i" :value="book.name">
            {{ book.name }}
          </option>
        </select>
      </div>
    </div>

    <div v-if="isLoading === false" class="mt-10 max-w-6xl">
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
          :disabled="
            (bookIndex === books.length - 1 &&
              currentChapter === books[bookIndex].chapters) ||
              isLoading
          "
          @click="fetchNext()"
        >
          Próximo
        </button>
      </div>
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
      version: "",
      books: [],
      bookIndex: 0,
      currentChapter: 1,
      bookName: "",
      verses: [],
      isLoading: true,
      newBook: "",
    };
  },
  watch: {
    version: {
      handler() {
        this.getData();
      },
    },
    newBook: {
      handler() {
        this.bookIndex = this.books.findIndex((e) => e.name === this.newBook);
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

    handleBookChange() {
      this.currentChapter = 1;
    },

    async getData() {
      this.isLoading = true;
      this.verses = [];

      const book = this.books[this.bookIndex];

      const res = await api.get(
        `verses/${this.version || "acf"}/${book.abbrev.pt}/${
          this.currentChapter
        }`
      );

      this.bookName = res.data.book.name;
      this.verses = res.data.verses;
      this.isLoading = false;
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
        this.newBook = book.name;
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
        this.newBook = book.name;
        this.currentChapter = book.chapters;
      }

      this.getData();
    },
  },
};
</script>
