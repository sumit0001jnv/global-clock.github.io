<template>
  <!-- <v-row class="pa-0 ma-0" no gutters justify="center" align="center"> -->
  <div :id="'clock-container' + country" class="clock-div pa-0 ma-0">
    <svg id="clock-svg"></svg>
  </div>
  <!-- </v-row> -->
</template>
<script>
import * as d3 from "d3";
export default {
  name: "Clock",
  data() {
    return {
      height: 0,
      width: 0,
      margin: { top: 32, bottom: 32, right: 32, left: 32 },
    };
  },
  props: {
    country: String,
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      this.createSvg();
    },
    createSvg() {
      let _this = this;
      // this.width = document.getElementById("clock-container").offsetWidth;
      // this.height = document.getElementById("clock-container").offsetHeight;
      this.width = 500;
      this.height = 500;
      let svg = d3
        .select("#clock-container" + this.country)
        .select("#clock-svg")
        .attr("width", this.width)
        .attr("height", this.height)
        .attr("transform", `translate(${0},${0})`);
      // .attr("transform", `translate(${0},${0})`);

      let radians = 0.0174532925,
        clockRadius =
          Math.min(
            this.width - this.margin.left - this.margin.right,
            this.height - this.margin.top - this.margin.bottom
          ) / 2,
        hourHandLength = (2 * clockRadius) / 3,
        minuteHandLength = clockRadius - 20,
        secondHandLength = clockRadius - 12,
        secondHandBalance = 30,
        secondTickStart = clockRadius,
        secondTickLength = -10,
        hourTickStart = clockRadius,
        hourTickLength = -18,
        secondLabelRadius = clockRadius + 16,
        secondLabelYOffset = 5,
        hourLabelRadius = clockRadius - 40,
        hourLabelYOffset = 7;

      let hourScale = d3
        .scaleLinear()
        .range([0, 330])
        .domain([0, 11]);

      let minuteScale, secondScale;
      minuteScale = secondScale = d3
        .scaleLinear()
        .range([0, 354])
        .domain([0, 59]);

      let handData = [
        {
          type: "hour",
          value: 0,
          length: -hourHandLength,
          scale: hourScale,
        },
        {
          type: "minute",
          value: 0,
          length: -minuteHandLength,
          scale: minuteScale,
        },
        {
          type: "second",
          value: 0,
          length: -secondHandLength,
          scale: secondScale,
          balance: secondHandBalance,
        },
      ];

      svg
        .append("circle")
        .attr("class", "circle-background")
        .attr("r", clockRadius)
        .attr("cx", _this.width / 2)
        .attr("cy", _this.height / 2);
      svg
        .append("image")
        // .attr("xlink:href", "statue-of-liberty-cp.png")
        .attr(
          "xlink:href",
          this.country === "India"
            ? "mahatma-gandhi.png"
            : "statue-of-liberty-cp.png"
        )
        // .attr("class", "statue-of-liberty-img")
        .attr(
          "class",
          this.country === "India" ? "mg-img" : "statue-of-liberty-img"
        );

      function drawClock() {
        //create all the clock elements
        updateData(); //draw them in the correct starting position
        var face = svg
          .append("g")
          .attr("id", "clock-face")
          .attr(
            "transform",
            "translate(" + _this.width / 2 + "," + _this.height / 2 + ")"
          );

        //add marks for seconds
        face
          .selectAll(".second-tick")
          .data(d3.range(0, 60))
          .enter()
          .append("line")
          .attr("class", "second-tick")
          .attr("x1", 0)
          .attr("x2", 0)
          .attr("y1", secondTickStart)
          .attr("y2", secondTickStart + secondTickLength)
          .attr("transform", function(d) {
            return "rotate(" + secondScale(d) + ")";
          });
        //and labels

        face
          .selectAll(".second-label")
          .data(d3.range(5, 61, 5))
          .enter()
          .append("text")
          .attr("class", "second-label")
          .attr("text-anchor", "middle")
          .attr("x", function(d) {
            return secondLabelRadius * Math.sin(secondScale(d) * radians);
          })
          .attr("y", function(d) {
            return (
              -secondLabelRadius * Math.cos(secondScale(d) * radians) +
              secondLabelYOffset
            );
          })
          .text(function(d) {
            return d;
          });

        //... and hours
        face
          .selectAll(".hour-tick")
          .data(d3.range(0, 12))
          .enter()
          .append("line")
          .attr("class", "hour-tick")
          .attr("x1", 0)
          .attr("x2", 0)
          .attr("y1", hourTickStart)
          .attr("y2", hourTickStart + hourTickLength)
          .attr("transform", function(d) {
            return "rotate(" + hourScale(d) + ")";
          });

        face
          .selectAll(".hour-label")
          .data(d3.range(3, 13, 3))
          .enter()
          .append("text")
          .attr("class", "hour-label")
          .attr("text-anchor", "middle")
          .attr("x", function(d) {
            return hourLabelRadius * Math.sin(hourScale(d) * radians);
          })
          .attr("y", function(d) {
            return (
              -hourLabelRadius * Math.cos(hourScale(d) * radians) +
              hourLabelYOffset
            );
          })
          .text(function(d) {
            return d;
          });

        var hands = face.append("g").attr("id", "clock-hands");

        face
          .append("g")
          .attr("id", "face-overlay")
          .append("circle")
          .attr("class", "hands-cover")
          .attr("x", 0)
          .attr("y", 0)
          .attr("r", clockRadius / 20);

        hands
          .selectAll("line")
          .data(handData)
          .enter()
          .append("line")
          .attr("class", function(d) {
            return d.type + "-hand";
          })
          .attr("x1", 0)
          .attr("y1", function(d) {
            return d.balance ? d.balance : 0;
          })
          .attr("x2", 0)
          .attr("y2", function(d) {
            return d.length;
          })
          .attr("transform", function(d) {
            return "rotate(" + d.scale(d.value) + ")";
          });
      }

      function moveHands() {
        svg
          .select("#clock-hands")
          .selectAll("line")
          .data(handData)
          .transition()
          .attr("transform", function(d) {
            return "rotate(" + d.scale(d.value) + ")";
          });
      }

      function updateData() {
        // let t = new Date();
        let t;
        if (_this.country !== "USA") {
          t = new Date().toLocaleString("en-US", {
            timeZone: "Asia/Kolkata",
          });
        } else {
          t = new Date().toLocaleString("en-US", {
            timeZone: "America/New_York",
          });
          // console.log("USA time: " + new Date(usaTime).toISOString());
        }
        t = new Date(t);

        _this.$emit("time-changed", t);

        // console.log("India time: " + new Date(indiaTime).toISOString());
        handData[0].value = (t.getHours() % 12) + t.getMinutes() / 60;
        handData[1].value = t.getMinutes();
        handData[2].value = t.getSeconds();
      }

      drawClock();

      setInterval(function() {
        updateData();
        moveHands();
      }, 1000);

      d3.select(self.frameElement).style("height", this.height + "px");
    },
  },
};
</script>
<style scoped>
.clock-div {
  /* width: 100%;
  height: 100%; */
}
</style>
<style>
svg {
  stroke: #000;
  font-family: "HelveticaNeue-Light", "Helvetica Neue Light", "Helvetica Neue",
    Helvetica, Arial, "Lucida Grande", sans-serif;
}

#rim {
  fill: none;
  stroke: #999;
  stroke-width: 3px;
  stroke: #ffaa2b;
}

.second-hand {
  stroke-width: 3;
  stroke: #e65a3d;
}

.minute-hand {
  stroke-width: 8;
  stroke-linecap: round;
  stroke: #ffaa2b;
}

.hour-hand {
  stroke-width: 12;
  stroke-linecap: round;
  stroke: #ffaa2b;
}

.hands-cover {
  stroke-width: 3;
  fill: #fff;
  stroke: #48b885;
}

.second-tick {
  stroke-width: 3;
  /* stroke: #394b59; */
  stroke: #48b885;
}

.hour-tick {
  stroke-width: 8;
  /* stroke: #394b59; */
  stroke: #48b885;
}

.second-label {
  font-size: 12px;
  stroke: #bd8c41;
  fill: #bd8c41;
}

.hour-label {
  font-size: 24px;
  stroke: #bd8c41;
  fill: #bd8c41;
}
.circle-background {
  fill: #d4f7e1;
  stroke: #48b885;
  stroke-width: 2px;
}
.statue-of-liberty-img {
  x: 167px;
  y: 92px;
  width: 175px;
  height: 317px;
}
.mg-img {
  x: 214px;
  y: 170px;
  width: 419px;
  height: 316px;
  clip-path: circle(42%);
  transform: translate(-172px, -76px);
}
</style>
