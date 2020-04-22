<template>
  <div class="note-edit">
    <form @submit="submitNote">
      <!-- <form> -->
      {{ this.modulTitle }}<br /><br />
      <table>
        <tr>
          <td style="width:100px">Заголовок:</td>
          <td style="width: 100%;">
            <input
              style="width: 100%;"
              id="title"
              v-model="title"
              placeholder="Заголовок..."
            /><br />
          </td>
        </tr>
        <tr>
          <td>Записка:</td>
          <td style="width: 100%;">
            <textarea
              style="width: 100%;"
              v-model="description"
              placeholder="Записка..."
            /><br />
          </td>
        </tr>
        <tr>
          <td>
            <label for="checkbox">Важная</label>
          </td>
          <td>
            <input type="checkbox" id="checkbox" v-model="is_favourite" />
          </td>
        </tr>
      </table>

      <input type="submit" value="Submit" class="btn btn-success" />
    </form>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      id: "",
      title: "",
      description: "",
      is_favourite: false,
      modulTitle: "",
    };
  },
  methods: {
    submitNote(e) {
      e.preventDefault();
      axios
        .post("http://ingroup.com/api/notes/create", {
          id: this.id,
          title: this.title,
          description: this.description,
          is_favourite: this.is_favourite,
        })
        .then(() => this.$router.push("/"));
    },
    getNote() {
      if (this.$route.query.id !== undefined) {
        axios
          .get(
            "http://ingroup.com/api/notes/get?filter[id][field]=id&filter[id][exp]==&filter[id][value]=" +
              this.$route.query.id
          )
          .then(({ data }) => {
            this.id = data.notes[0].id;
            this.title = data.notes[0].title;
            this.description = data.notes[0].description;
            this.is_favourite = data.notes[0].is_favourite == 1 ? true : false;
            this.modulTitle = "Редактирование записки";
          })
          .catch((err) => console.log(err));
      } else {
        this.modulTitle = "Новая записка";
      }
    },
  },
  beforeMount() {
    this.getNote();
  },
};
</script>

<style scoped>
.note-edit {
  border-radius: 10px;
  padding: 10px;
  background-color: lightgreen;
  width: 60%;
  min-width: 500px;
  margin: auto;
  text-align: left;
}
</style>
