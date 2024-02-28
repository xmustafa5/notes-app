<script setup>
import { ref } from "vue";
const isOpen = ref(false);
const isOpenDetails = ref(false);
const details = ref();
const title = ref("");
const newNote = ref("");
const errorMsg = ref("");
let existingNotes = JSON.parse(localStorage.getItem("notes")) || [];

const notes = ref(existingNotes);
function getRandomColor() {
  return "hsl(" + Math.random() * 360 + ", 100%, 75%)";
}
function handlingTheDate() {
  const date = new Date();
  const Years = date.getFullYear().toString();
  const Month = (date.getMonth() + 1).toString().padStart(2, "0");
  const Day = (date.getDay() + 1).toString().padStart(2, "0");
  const Hours = date.getHours().toString();
  const Minutes = date.getMinutes().toString();
  return `${Years} / ${Month} / ${Day} - ${Hours} / ${Minutes} `;
}
const addNote = () => {
  if (newNote.value.length < 10) {
    return (errorMsg.value = "Note must be 10 characters or more");
  }

  let existingNotes = JSON.parse(localStorage.getItem("notes")) || [];

  existingNotes.push({
    id: Math.floor(Math.random() * 100000),
    title: title.value,
    text: newNote.value,
    date: handlingTheDate(),
    bgColor: getRandomColor(),
  });
  notes.value = existingNotes;
  localStorage.setItem("notes", JSON.stringify(existingNotes));
  notes.value = existingNotes;
  isOpen.value = false;
  newNote.value = "";
  errorMsg.value = "";
};

function handlingOpenModel() {
  isOpen.value = !isOpen.value;
}

console.log(existingNotes, "outSide");
function deleteNote(id) {
  console.log(id);
  existingNotes = existingNotes.filter((note) => note.id !== id);
  const notess = notes.value.filter((note) => note.id !== id);
  console.log(existingNotes, "existingNotes");
  localStorage.setItem("notes", JSON.stringify(existingNotes));
  notes.value = notess;
}

function handlingOpenDetailsOfNote(note) {
  console.log("test");
  details.value = note;
  isOpenDetails.value = !isOpenDetails.value;
}
</script>

<template>
  <main>
    <div class="overlay" v-if="isOpen" @click.self="handlingOpenModel()">
      <div class="modal">
        \
        <input type="text" name="title" v-model="title" />
        <textarea
          v-model.trim="newNote"
          name="note"
          id="note"
          cols="30"
          rows="10"
        ></textarea>
        <p v-if="errorMsg">{{ errorMsg }}</p>
        <button class="add" @click="addNote()">Add Note</button>
        <button class="close" @click="handlingOpenModel()">close</button>
      </div>
    </div>
    <div
      class="overlayNote"
      v-if="isOpenDetails"
      @click.self="handlingOpenDetailsOfNote()"
    >
      <div class="modalNote">
        <h1>{{ details.title }}</h1>
        <div class="containerP">
          <p>{{ details.text }}.</p>
        </div>
        <button @click="handlingOpenDetailsOfNote()">close</button>
      </div>
    </div>
    <div class="container">
      <header>
        <h1>Notes</h1>
        <button @click="handlingOpenModel()">+</button>
      </header>
      <div class="cards-container">
        <div
          class="card"
          :key="note.id"
          v-for="note in notes"
          :style="{ backgroundColor: note.bgColor }"
        >
          <div class="containerUpCard" @click="handlingOpenDetailsOfNote(note)">
            <h4>{{ note.title }}</h4>
            <div class="upCard">
              <p class="main-text">
                {{ note.text }}
              </p>
            </div>
          </div>
          <div>
            <p class="data">{{ note.date }}</p>
          </div>
          <button class="deleted" @click.self="deleteNote(note.id)">X</button>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
main {
  height: 100vh;
  width: 100vw;
}
.container {
  max-width: 1000px;
  padding: 10px;
  margin: 0 auto;
}
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
h1 {
  font-weight: bold;
  margin-bottom: 25px;
  font-size: 75px;
}
.containerUpCard {
  height: 80%;
  cursor: pointer;
}
.upCard {
  display: flex;
  justify-content: start;
  align-items: start;
  flex-direction: column;
  overflow-y: auto;
  height: 90%;
}
.upCard::-webkit-scrollbar {
  width: 8px; /* width of the scrollbar */
}

.upCard::-webkit-scrollbar-track {
  background: rgb(158, 146, 146); /* color of the track */
  border-radius: 10px;
}

.upCard::-webkit-scrollbar-thumb {
  background: white; /* color of the scrollbar */
}

.downCard {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
header button {
  border: none;
  padding: 10px;
  width: 50px;
  height: 50px;
  background-color: rgb(21, 20, 20);
  border-radius: 100%;
  color: white;
  font-size: 20px;
}
.card {
  width: 225px;
  height: 225px;
  background-color: rgb(237, 178, 44);
  padding: 10px;
  border-radius: 15px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  margin-right: 20px;
  margin-bottom: 20px;
  position: relative;
}
.date {
  font-size: 12.5px;
  font-weight: 500;
}
.cards-container {
  display: flex;
  flex-wrap: wrap;
}
.overlay {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgb(0, 0, 0, 0.77);
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;
}
.overlayNote {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgb(0, 0, 0, 0.77);
  z-index: 10;
  direction: rtl;
}
.modal {
  width: 750px;
  background-color: white;
  border-radius: 10px;
  padding: 30px;
  position: relative;
  display: flex;
  flex-direction: column;
}

.modal button {
  padding: 10px 20px;
  font-size: 20px;
  width: 100%;
  background-color: blueviolet;
  border: none;
  color: white;
  cursor: pointer;
  margin-top: 15px;
}
.modal .close {
  background-color: rgb(193, 15, 15) !important;
  margin-top: 7px;
}

.modal p {
  color: red;
}
.deleted {
  padding: 10px 14px;
  border: none;
  background: red;
  border-radius: 100%;
  color: white;
  font-weight: 600;
  cursor: pointer;
  position: absolute;
  top: -30px;
  right: -30px;
}
.modalNote {
  direction: ltr;
  background: white;
  width: 70vw;
  height: 100vh;
  display: flex;
  justify-content: space-around;
  align-items: start;
  flex-direction: column;
  padding: 20px;
}
.modalNote div {
  border: 1px solid gray;
  width: 600px;
  height: 300px;
  padding: 10px 3px;
}
.modalNote button {
  border: none;
  padding: 10px 30px;
  color: white;
  font-weight: 600;
  background: red;
  border-radius: 6px;
}
</style>
