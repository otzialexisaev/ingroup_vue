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
      page: 1,
      count: 10,
      notes: [],
      dontAppend: false, // если приходит пустой массив с данными то можно больше его не запрашивать
      sort: null,
      direction: null,
    };
  },
  methods: {
    sortByDate() {
      this.sort = "created_at";
      this.direction = this.direction == "DESC" ? "ASC" : "DESC";
      this.page = 1;
      axios
        .get(
          "http://ingroup.com/api/notes/get?page=" +
            this.page +
            "&count=" +
            this.count +
            "&sort[field]=" +
            this.sort +
            "&sort[direction]=" +
            this.direction
        )
        .then(({ data }) => {
          this.clearNotes();
          this.appendNotes(data.notes);
        })
        .catch((err) => console.log(err));
    },
    sortByFavourite() {
      this.sort = "is_favourite";
      this.direction = this.direction == "DESC" ? "ASC" : "DESC";
      this.page = 1;
      axios
        .get(
          "http://ingroup.com/api/notes/get?page=" +
            this.page +
            "&count=" +
            this.count +
            "&sort[field]=" +
            this.sort +
            "&sort[direction]=" +
            this.direction
        )
        .then(({ data }) => {
          this.clearNotes();
          this.appendNotes(data.notes);
        })
        .catch((err) => console.log(err));
    },
    clearNotes() {
      this.notes = [];
      this.dontAppend = false;
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
    loadNotes() {
      if (this.dontAppend !== true) {
        let url =
          "http://ingroup.com/api/notes/get?page=" +
          this.page +
          "&count=" +
          this.count;
        if (this.sort != null) url += "&sort[field]=" + this.sort;
        if (this.direction != null) url += "&sort[direction]=" + this.direction;
        axios
          .get(url)
          .then((res) => {
            if (res.data.notes.length > 0) {
              this.appendNotes(res.data.notes);
            } else {
              // не осталось данных - не запрашивать больше
              this.dontAppend = true;
            }
          })
          .catch((err) => {
            console.log(err);
          });
      }
    },
    incrementPage() {
      // увеличиваем страницу для догрузки
      this.page = ++this.page;
    },
    decrementPage() {
      this.page = --this.page;
    },
    // initScrollCheck() {
    // if ()
    // },

    attachScrollListener() {
      // console.log(
      //   document.documentElement.scrollTop + window.innerHeight ===
      //     document.documentElement.offsetHeight
      // );
      // console.log("scrollTop: " + document.documentElement.scrollTop);
      // console.log("window.innerHeight: " + window.innerHeight);
      // console.log("offsetHeight: " + document.documentElement.offsetHeight);
      // console.log("clientHeight: " + document.documentElement.clientHeight);

      // if (window.innerHeight >= document.documentElement.clientHeight - 2)
      // this.loadNotes();

      window.onscroll = async () => {
        // console.log("scrollTop: " + document.documentElement.scrollTop);
        // console.log("window.innerHeight: " + window.innerHeight);
        // console.log("offsetHeight: " + document.documentElement.offsetHeight);
        let bottomOfWindow =
          document.documentElement.scrollTop + window.innerHeight >=
          document.documentElement.offsetHeight;

        // console.log(bottomOfWindow);

        if (bottomOfWindow) {
          await this.loadNotes();
        }
      };
    },
    // async fillPage() {
    //   if (window.innerHeight >= document.documentElement.clientHeight - 2)
    //     await this.loadNotes().then(async () => {
    //       await this.fillPage();
    //     });
    //   else return;
    // },
  },
  beforeMount() {
    // await this.loadNotes().then(() => {
    //   if (window.innerHeight >= document.documentElement.offsetHeight - 2)
    //     this.loadNotes();
    // });
    this.loadNotes();
    // await this.fillPage();
    // await this.loadNotes().then(async () => {
    //   // изначальная проверка (если заметок так мало что даже скроллить нельзя то onscroll не сработает и новые заметки не загрузить)
    //   // 2 пикселя запас потому что при приближении в браузере числа могут чуть не совпасть
    //   // while (test) {
    //   if (window.innerHeight >= document.documentElement.clientHeight - 2)
    //     await this.loadNotes();
    //   // }
    // });
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
