<template>
  <v-container fill-height fluid>
    <v-row align="center" justify="center">
      <v-col class="col-12 col-md-6 col-lg-3">
        <v-card elevation="20" height="600" tile>
          <v-row class="top-card white--text pt-5 pb-5" no-gutters>
            <v-col cols="3" class="text-center center-children" no-gutters>
              <v-btn icon @click="RemoveAll"
                ><v-icon size="30" color="white">
                  mdi-arrow-left
                </v-icon>
              </v-btn>
            </v-col>
            <v-col cols="6" class="text-center center-children">
              <v-row no-gutters class="text-h6 font-weight-medium">
                {{ Name }}
              </v-row>
              <v-row
                v-if="List.length === 1"
                no-gutters
                class="font-weight-light font-small"
              >
                {{ List.length }} Task
              </v-row>
              <v-row
                v-else-if="List.length > 0"
                no-gutters
                class="font-weight-light font-small"
              >
                {{ List.length }} Tasks
              </v-row>
            </v-col>
          </v-row>

          <v-container
            class="middle-card scroll-list pl-10"
            align="start"
            no-gutters
          >
            <v-row v-for="i in List.length" :key="i" class="todo-entry">
              <v-col
                cols="10"
                class=" center-children-vertical"
                :class="{
                  'active-text': !List[i - 1].Done,
                  'removed-text': List[i - 1].Done
                }"
                >{{ List[i - 1].Text }}</v-col
              >
              <v-col cols="2" class="center-children-all">
                <v-icon
                  v-if="!List[i - 1].Done"
                  x-large
                  @click="ToggleDone(i)"
                  color="#dddee0"
                >
                  mdi-checkbox-blank-circle-outline
                </v-icon>
                <v-icon v-else @click="ToggleDone(i)" color="#2ac782" x-large>
                  mdi-checkbox-marked-circle-outline
                </v-icon>
              </v-col>
            </v-row>
          </v-container>

          <v-row class="bottom-card pa-5 pt-0 pb-0" no-gutters>
            <v-col v-if="NewInput" cols="12">
              <v-text-field
                id="inputval"
                v-model="message"
                placeholder="New Entry"
                hide-details="auto"
                append-icon="mdi-plus-circle"
                @keypress="checkEnter($event)"
                @click:append="toggleinput"
              ></v-text-field>
            </v-col>
            <v-col v-else offset="9" cols="2" class="center-children-all">
              <v-btn icon @click="InsertInput">
                <v-icon color="#98c4f3" size="80">
                  mdi-plus-circle
                </v-icon>
              </v-btn>
            </v-col>
          </v-row>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "TodoList",
  data: () => ({
    List: [],
    NewInput: false,
    message: ""
  }),
  methods: {
    toggleinput() {
      if (this.message !== "")
        this.List.push({ Text: this.message, Done: false });
      this.message = "";
      this.NewInput = false;
      localStorage.setItem("TodoList", JSON.stringify(this.List));
    },
    ToggleDone(i) {
      this.List[i - 1].Done = !this.List[i - 1].Done;
      localStorage.setItem("TodoList", JSON.stringify(this.List));
    },
    RemoveAll() {
      this.List = [];
      localStorage.setItem("TodoList", JSON.stringify(this.List));
    },
    checkEnter(e) {
      if (e.keyCode === 13) this.toggleinput();
    },
    InsertInput() {
      this.NewInput = true;
      this.$nextTick(() => {
        document.getElementById("inputval").focus();
      });
    }
  },
  created() {
    this.List = JSON.parse(localStorage.getItem("TodoList"));
    if (this.List === null) this.List = [];
  },
  computed: {
    Name() {
      const Days = ["Sun", "Mon", "Tues", "Wed", "Thu", "Fri", "Sat"];
      const Months = [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "June",
        "July",
        "Aug",
        "Sep",
        "Oct",
        "Nov",
        "Dec"
      ];
      var today = new Date();
      var mm = String(today.getMonth() + 1).padStart(2, "0");
      var yyyy = today.getFullYear();
      return String(
        Days[today.getDay()] +
          ", " +
          Months[Number(mm) - 1] +
          " " +
          mm +
          ", " +
          String(yyyy)
      );
    }
  }
};
</script>

<style scoped>
.top-card {
  background-image: url(../assets/Top-Background.svg);
  background-size: 100% 100%;
  height: 18%;
}
.middle-card {
  height: 64%;
}
.bottom-card {
  height: 18%;
}
.todo-entry {
  height: 60px;
  margin-right: 10px;
}
.center-children {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
}
.center-children-all {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.center-children-vertical {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start;
}
.scroll-list {
  overflow: auto;
  color: #5c6260;
}
.active-text {
  color: #6a6c6b;
  text-decoration: none;
}
.removed-text {
  color: #dddee0;
  text-decoration: line-through;
}
.font-small {
  font-size: 13px;
  margin: 0px;
}
</style>
