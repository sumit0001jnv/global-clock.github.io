<template>
  <v-row class="pa-0 ma-0" no-gutters justify="center" align="center">
    <div
      class="d-dlex column justify-center pa-4"
      v-for="(country, i) in countries"
      :key="i"
    >
      <Clock
        :country="country"
        @time-changed="onTimeChanged($event, country)"
      ></Clock>
      <div class="text-center country-div d-dlex column justify-center banner">
        {{ country === "India" ? indianTime : usaTime }}
        <div>
          {{ country }}
        </div>
      </div>
    </div>
  </v-row>
</template>
<script>
import Clock from "./Clock";
export default {
  name: "CountryClock",
  components: {
    Clock,
  },
  data() {
    return { countries: ["India", "USA"], usaTime: "", indianTime: "" };
  },
  //   computed: {
  //     getIndianTime() {
  //       if (!this.indianTime) {
  //         this.indianTime = new Date();
  //       }
  //       return `${this.indianTime.getHours() %
  //         12} : ${this.indianTime.getMinutes()} : ${this.indianTime.getSeconds()}`;
  //     },
  //     getUSATime() {
  //       if (!this.usaTime) {
  //         this.usaTime = new Date();
  //       }
  //       return `${this.usaTime.getHours() %
  //         12} : ${this.usaTime.getMinutes()} : ${this.usaTime.getSeconds()}`;
  //     },
  //   },
  methods: {
    getTime(time) {
      let hr = time.getHours() % 12;
      let min = time.getMinutes();
      let sec = time.getSeconds();
      hr = hr < 10 ? "0" + hr : hr;
      min = min < 10 ? "0" + min : min;
      sec = sec < 10 ? "0" + sec : sec;
      return `${hr} : ${min} : ${sec}${time.getHours() >= 12 ? " PM" : " AM"}`;
    },
    onTimeChanged(time, country) {
      if (country === "India") {
        this.indianTime = this.getTime(time);
      } else {
        this.usaTime = this.getTime(time);
      }
    },
  },
};
</script>
<style scoped>
.country-div {
  color: #293742;
  font-family: Roboto;
  font-style: normal;
  font-weight: 500;
  font-size: 20px;
  line-height: 32px;
}
.banner {
  background-color: #d4f7e1;
  border: 0.3px solid #293742;
  border-radius: 4px;
}
</style>
