<template>
  <div class="home">
    <NotesTop
      @sort-by-date="sortByDate"
      @sort-by-favourite="sortByFavourite"
    ></NotesTop>
    <div :key="note.id" v-for="note in notes">
      <Note @delete-note="deleteNote" :note="note"></Note>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Note from "@/components/Note.vue";
import NotesTop from "@/components/NotesTop.vue";
import axios from "axios";

export default {
  name: "Home",
  components: {
    Note,
    NotesTop,
  },
  data() {
    return {
      config: {
        page: 1,
        count: 10,
        dontAppend: false, // если приходит пустой массив с данными то можно больше его не запрашивать
        sort: null,
        direction: null,
        bClearNotes: false,
      },
      notes: [],
    };
  },
  methods: {
    // грузим заметки в зависимости от конфига
    loadNotes() {
      if (this.config.dontAppend !== true) {
        let url =
          "http://ingroup.com/api/notes/get?pager[page]=" +
          this.config.page +
          "&pager[count]=" +
          this.config.count;
        if (this.config.sort != null) url += "&sort[field]=" + this.config.sort;
        if (this.config.direction != null)
          url += "&sort[direction]=" + this.config.direction;
        axios
          .get(url)
          .then((res) => {
            if (this.config.bClearNotes) this.clearNotes();
            if (res.data.notes.length > 0) {
              this.appendNotes(res.data.notes);
            } else {
              // не осталось данных - не запрашивать больше
              this.config.dontAppend = true;
            }
          })
          .catch((err) => {
            console.log(err);
          });
      }
    },
    sortByDate() {
      this.config.sort = "created_at";
      this.config.direction = this.config.direction == "DESC" ? "ASC" : "DESC";
      this.config.page = 1;
      this.config.bClearNotes = true;
      this.config.dontAppend = false;
      this.loadNotes();
    },
    sortByFavourite() {
      this.config.sort = "is_favourite";
      this.config.direction = this.config.direction == "DESC" ? "ASC" : "DESC";
      this.config.page = 1;
      this.config.bClearNotes = true;
      this.config.dontAppend = false;
      this.loadNotes();
    },
    clearNotes() {
      this.notes = [];
      this.config.dontAppend = false;
      this.config.bClearNotes = false;
    },
    deleteNote(note) {
      axios
        .get("http://ingroup.com/api/notes/delete?id=" + note.id)
        .then((res) => {
          if (res.data.success === 1) {
            this.notes.splice(this.notes.indexOf(note), 1);
          }
        })
        .catch((err) => console.log(err));
    },
    appendNotes(notesArr) {
      this.notes.push(...notesArr);
      this.incrementPage();
    },
    incrementPage() {
      // увеличиваем страницу для догрузки
      this.config.page = ++this.config.page;
    },
    decrementPage() {
      this.config.page = --this.config.page;
    },
    attachScrollListener() {
      window.onscroll = async () => {
        let bottomOfWindow =
          document.documentElement.scrollTop + window.innerHeight >=
          document.documentElement.offsetHeight;
        if (bottomOfWindow) {
          await this.loadNotes();
        }
      };
    },
  },
  beforeMount() {
    this.loadNotes();
  },
  mounted() {
    this.attachScrollListener();
    // this.attachScrollListener();
  },
  watch: {
    $route: () => {
      this.loadNotes();
    },
  },
};
</script>

<style scoped>
.home {
  width: 60%;
  min-width: 500px;
  margin: auto;
}
</style>
