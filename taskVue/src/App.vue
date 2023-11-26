<template>
  <div className="app">
    <h2>Vazifalar menedjeri</h2>
    <div className="add">
      <form className="add-task" @submit.prevent>
        <input
          className="input"
          placeholder="Yangi vazifa qo'shish"
          type="text"
          @input="newTask = $event.target.value"
        />
        <button @click="getData">+</button>
      </form>
      <div className="date">
        <span className="dates">{{ currentTime }}</span>
      </div>
    </div>

    <div className="today">
      <h1 @click="ha" :class="[{ dNone: !today.length }]">Bugun</h1>
      <Task v-for="days in today" :days="days" @mark="todayMark" />
    </div>

    <div className="tomorrow">
      <h1 @click="ha" :class="[{ dNone: !tomorrow.length }]">Ertaga</h1>
      <Task v-for="days in tomorrow" :days="days" @mark="tomorrowMark" />
    </div>

    <div className="afterTomorrow">
      <h1 @click="ha" :class="[{ dNone: !afterTomorrow.length }]">Keyin</h1>
      <Task v-for="days in afterTomorrow" :days="days" @mark="afterMark" />
    </div>

    <div className="statistics">
      <span className="number-completed"
        >Bajarilganlar soni: {{ counts.y }}</span
      >
      <span className="number-uncompleted">
        Bajarilmaganlar soni: {{ counts.n }}</span
      >
    </div>
  </div>
</template>

<script>
import Task from "./components/Task.vue";
export default {
  components: {
    Task,
  },
  mounted() {
    this.everyDate();
  },
  methods: {
    everyDate() {
      setInterval(() => {
        const date = new Date();
        const year = date.getFullYear();
        const month = date.getMonth();
        const days = date.getDate();
        const hour = date.getHours();
        const minute = date.getMinutes();
        const second = date.getSeconds();
        this.currentTime = `Bugun: ${days}.${month}.${year}, ${hour}:${minute}:${second}`;
      }, 1000);
    },

    sortData() {
      this.today.sort((a, b) => a.time.split(":")[0] - b.time.split(":")[0]);
    },
    todayMark(e) {
      this.today.map((el) => {
        if (el.id === e) {
          el.completed = !el.completed;
          return el;
        }
        return el;
      });
      const count = { y: 0, n: 0 };
      this.today.forEach((element) => {
        element.completed ? (count.y += 1) : (count.n += 1);
      });
      this.tomorrow.forEach((element) => {
        element.completed ? (count.y += 1) : (count.n += 1);
      });
      this.afterTomorrow.forEach((element) => {
        element.completed ? (count.y += 1) : (count.n += 1);
      });
      this.counts = count;
    },
    tomorrowMark(e) {
      this.tomorrow.map((el) => {
        if (el.id === e) {
          el.completed = !el.completed;
          return el;
        }
        return el;
      });

      const count = { y: 0, n: 0 };
      this.today.forEach((element) => {
        element.completed ? (count.y += 1) : (count.n += 1);
      });
      this.tomorrow.forEach((element) => {
        element.completed ? (count.y += 1) : (count.n += 1);
      });
      this.afterTomorrow.forEach((element) => {
        element.completed ? (count.y += 1) : (count.n += 1);
      });
      this.counts = count;
    },
    afterMark(e) {
      this.afterTomorrow.map((el) => {
        if (el.id === e) {
          el.completed = !el.completed;
          return el;
        }
        return el;
      });
      const count = { y: 0, n: 0 };
      this.today.forEach((element) => {
        element.completed ? (count.y += 1) : (count.n += 1);
      });
      this.tomorrow.forEach((element) => {
        element.completed ? (count.y += 1) : (count.n += 1);
      });
      this.afterTomorrow.forEach((element) => {
        element.completed ? (count.y += 1) : (count.n += 1);
      });
      this.counts = count;
    },

    getData() {
      const date = new Date();
      const year = date.getFullYear();
      const month = date.getMonth();
      const days = date.getDate();
      const hour = date.getHours();
      const minute = date.getMinutes();

      const text = this.newTask;
      if (text !== null && text !== "") {
        const dateRegex =
          /^(0?[1-9]|[12][0-9]|3[01])\.(0?[0-9]|1[0-9])\.\d{4}$/;
        const timeRegex = /^(0?[0-9]|1[0-9]|2[0-3]):[0-5][0-9]$/;
        const todayRegex = /\b(bugun)\b/i;
        const tomorrowRegex = /\b(ertaga)\b/i;

        let splText = text.split(" ");
        splText = splText.map((e) => e.toLowerCase());
        const Days = [todayRegex, tomorrowRegex, dateRegex];
        Days.forEach((day, i) => {
          if (splText[0].match(day)) {
            if (splText.length >= 2 && splText.at(-1).match(timeRegex)) {
              (i == 0
                ? this.today
                : i == 1
                ? this.tomorrow
                : this.afterTomorrow
              ).push({
                event: splText.slice(1, -1).join(" "),
                time:
                  i == 2
                    ? `${splText[0]},` + splText.at(-1).split(":")[1] > 0
                      ? `${splText[0]}, ${+splText.at(-1).split(":")[0] + 1}:00`
                      : `${splText[0]}, ${splText.at(-1).split(":")[0]}:${
                          splText.at(-1).split(":")[1]
                        }`
                    : splText.at(-1).split(":")[1] > 0
                    ? `${+splText.at(-1).split(":")[0] + 1}:00`
                    : `${splText.at(-1).split(":")[0]}:${
                        splText.at(-1).split(":")[1]
                      }`,
                completed: false,
                id: Date.now(),
              });
              return;
            }
            (i == 0
              ? this.today
              : i == 1
              ? this.tomorrow
              : this.afterTomorrow
            ).push({
              event: splText.slice(1).join(" "),
              time:
                i == 1
                  ? "9:00"
                  : i == 2
                  ? `${splText[0]}, 9:00`
                  : minute > 0
                  ? `${hour + 1}:00`
                  : `${hour}:${minute}`,
              completed: false,
              id: Date.now(),
            });
            return;
          }
          if (splText.length >= 1 && splText.at(-1).match(day)) {
            (i == 0
              ? this.today
              : i == 1
              ? this.tomorrow
              : this.afterTomorrow
            ).push({
              event: splText.slice(0, -1).join(" "),
              time:
                i == 2
                  ? `${splText.at(-1)}, 9:00`
                  : i == 1
                  ? "9:00"
                  : minute > 0
                  ? `${hour + 1}:00`
                  : `${hour}:${minute}`,
              completed: false,
              id: Date.now(),
            });
            return;
          }
          if (
            splText.length >= 2 &&
            splText.at(-2).match(day) &&
            splText.at(-1).match(timeRegex)
          ) {
            (i == 0
              ? this.today
              : i == 1
              ? this.tomorrow
              : this.afterTomorrow
            ).push({
              event: splText.slice(0, -2).join(" "),
              time:
                i == 2
                  ? splText.at(-1).split(":")[1] > 0
                    ? `${splText.at(-2)}, ${
                        +splText.at(-1).split(":")[0] + 1
                      }:00`
                    : `${splText.at(-2)}, ${splText.at(-1).split(":")[0]}:${
                        splText.at(-1).split(":")[1]
                      }`
                  : splText.at(-1).split(":")[1] > 0
                  ? `${+splText.at(-1).split(":")[0] + 1}:00`
                  : `${splText.at(-1).split(":")[0]}:${
                      splText.at(-1).split(":")[1]
                    }`,
              completed: false,
              id: Date.now(),
            });
            return;
          }

          if (
            i > 1 &&
            !splText.at(0).match(todayRegex) &&
            !splText.at(0).match(tomorrowRegex) &&
            !splText.at(0).match(dateRegex) &&
            !splText.at(-1).match(timeRegex) &&
            !splText.at(-1).match(todayRegex) &&
            !splText.at(-1).match(tomorrowRegex) &&
            !splText.at(-1).match(dateRegex)
          ) {
            this.today.push({
              event: text,
              time: minute > 0 ? `${hour + 1}:00` : `${hour}:${minute}`,
              completed: false,
              id: Date.now(),
            });
            return;
          }
        });
      }
      this.today.sort((a, b) => a.time.split(":")[0] - b.time.split(":")[0]);
      this.tomorrow.sort((a, b) => a.time.split(":")[0] - b.time.split(":")[0]);
      this.afterTomorrow.sort(
        (a, b) => a.time.split(":")[0] - b.time.split(":")[0]
      );
      const count = { y: 0, n: 0 };
      this.today.forEach((element) => {
        element.completed ? (count.y += 1) : (count.n += 1);
      });
      this.tomorrow.forEach((element) => {
        element.completed ? (count.y += 1) : (count.n += 1);
      });
      this.afterTomorrow.forEach((element) => {
        element.completed ? (count.y += 1) : (count.n += 1);
      });
      this.counts = count;
    },
  },
  data() {
    return {
      currentTime: "",
      newTask: "",
      counts: { y: 0, n: 0 },
      today: [],
      tomorrow: [],
      afterTomorrow: [],
    };
  },
};
</script>

<style>
.app {
  max-width: 500px;
  border-radius: 10px;
  display: flex;
  justify-content: center;
  flex-direction: column;
  margin: 0 auto;
  font-family: "Roboto", sans-serif;
  padding: 40px 20px;
}

.dNone {
  display: none;
  opacity: 0;
  pointer-events: none;
  z-index: -999;
}

@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@100;200;300;400;500;600;800;900&display=swap");

body {
  font-family: "Roboto", sans-serif;
}

.app h2 {
  font-size: 28px;
  text-align: center;
}

.add {
  width: 100%;
}

.add-task {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.add-task input {
  width: 80%;
  padding: 6px 20px 6px 10px;
  font-size: 20px;
  border: rgb(178, 177, 177) 2.5px solid;
  border-radius: 5px;
}

.add-task input::placeholder {
  color: rgba(159, 158, 158, 0.706);
}

.add-task input:focus {
  border-color: rgb(9, 221, 9) !important;
  outline: none !important;
  box-shadow: 0 0 10px #71ce96;
}
.add-task button {
  width: 42px;
  font-size: 25px;
  height: 38px;
  box-shadow: 0 0 10px #71ce96;
  border: #4eb17a solid 2.5px;
  border-radius: 4px;
  background-color: #89d7ad;
  transition: transform 0.2s, background-color 0.3s, color 0.4s ease;
  cursor: pointer;
}

.add-task button:hover {
  box-shadow: 0 0 20px 4px #71ce96;
  background-color: #4eb17a;
  color: white;
}

.add-task button:active {
  transform: scale(1.2);
}

.date {
  padding-left: 5px;
  font-weight: 500;
  color: #727272;
}

/* -------- task -------- */

.task {
  display: flex;
  justify-content: space-between;
  padding: 0px 10px;
  user-select: none;
}

.task-info {
  display: flex;
  justify-content: center;
  align-items: center;
}

.task-info input {
  position: relative;
  border-radius: 2px;
  padding: 8px;
  background: #f6f6f6;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  border: 2.5px solid #c5c5c5;
}

.completed {
  color: #757575;
  text-decoration: line-through;
}

.completed input {
  background: #56b8ff;
  border-color: #56b8ff;
}

/* -------- statistics -------- */

.statistics {
  display: flex;
  flex-direction: column;
  padding: 50px 10px 0px 0px;
  justify-content: end;
  align-items: end;
  color: #b9b9b9;
  font-size: 12px;
}
</style>
