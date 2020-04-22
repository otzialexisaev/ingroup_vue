<template>
  <div
    class="note"
    v-bind:class="{ 'note-favourite': note.is_favourite == 1 }"
    @click="$router.push(editUrl)"
  >
    <div class="note-insides">
      <div class="note-top">
        <div class="note-title">{{ note.title }}</div>
        <div class="note-created-at">{{ note.created_at }}</div>
      </div>
      <!-- {{ note.id }} -->

      <div class="note-body">
        <div class="note-description">{{ note.description }}</div>
        <!-- {{ note.created_at }} -->
        <!-- {{ note.is_favourite }} -->
        <div class="note-btn-container">
          <button
            class="btn btn-danger note-corner-btn"
            @click="deleteNote($event)"
          >
            Удалить
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["note"], // объект с полями как в базе
  methods: {
    deleteNote(event) {
      this.$emit("delete-note", this.$props.note);
      event.stopPropagation();
    },
  },
  data() {
    return {
      editUrl: "editNote?id=" + this.$props.note.id,
    };
  },
};
</script>

<style scoped>
.note-btn-container {
  position: relative;
  width: 72px;
}
.note-insides {
  height: 100%;
  display: flex;
  flex-direction: column;
}
.note-corner-btn {
  position: absolute;
  bottom: 0px;
  right: 0px;
  width: 72px;
  padding: 1px;
  height: 30px;
}
.note-top {
  display: flex;
}
.note-body {
  display: flex;
  height: 100%;
  overflow: hidden;
}
.note-description {
  flex: 1;
  overflow: hidden;
}
.note-link {
  text-decoration: none;
}
.note-title {
  font-style: italic;
  text-align: left;
  flex: 1;
  overflow: hidden;
}
.note {
  position: relative;
  background-color: lightblue;
  margin-bottom: 10px;
  border-radius: 10px;
  padding: 15px;
  height: 100px;
}
.note:hover {
  background-color: rgb(100, 150, 241);
}
.note-favourite {
  background-color: lightyellow;
}
</style>
