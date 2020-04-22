<template>
  <div class="notes-top">
    <div class="note-create-btn-container">
      <button
        @click="$router.push('/editNote')"
        class="btn btn-success top-notes-buttons"
      >
        Создать
      </button>
    </div>
    <div class="note-sort-btns-container">
      <button
        class="btn btn-success notes-sort-buttons top-notes-buttons"
        @click="sortByDate"
      >
        Сортировка по дате
      </button>
      <button
        class="btn btn-success notes-sort-buttons top-notes-buttons"
        @click="sortByFavourite"
      >
        Сортировка по избранному
      </button>
    </div>
  </div>
</template>

<script>
export default {
  methods: {
    sortByDate() {
      this.$emit("sort-by-date");
    },
    sortByFavourite() {
      this.$emit("sort-by-favourite");
    },
  },
  components: {},
};
</script>

<style scoped>
.top-notes-buttons {
  margin: 2px 5px 0px 5px;
  height: 40px;
}
.notes-sort-buttons {
  font-size: 0.8em;
}
.notes-top {
  width: 100%;
  background-color: lightgreen;
  height: 45px;
  margin-bottom: 15px;
  display: flex;
  padding: 0px 5px 0px 5px;
}
.note-sort-btns-container {
  text-align: right;
  flex: 1;
}
</style>
