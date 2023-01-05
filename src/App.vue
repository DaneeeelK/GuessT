<template>
  <main>
    <button @click="changeCity">Change city</button>
    <div class="citiesGrid">
      <div class="placeBox" v-for="(city, index) in choosenFour">
        <p>City: {{ city.name }},{{ city.sys.country }}</p>
        <p>Your guess:{{ usersGuess[index] }}</p>
        <p>Actual:{{ Math.round(choosenFour[index].main.temp) }}</p>
        <input v-show="!resultBox" type="number" v-model="usersGuess[index]" />
      </div>
      <div class="btn" @click="compareAnsw">
        <button v-show="choosenFour.length === 4">Done</button>
      </div>
    </div>
    <div class="results" v-show="resultBox">
      <div class="resultbox badres" :class="{ active: resultFlagBad }">
        <p>Bad result</p>
        <p>Your average miss is >5°</p>
      </div>
      <div class="resultbox okres" :class="{ active: resultFlagOk }">
        <p>Good result</p>
        <p>Your average miss is 3-5°</p>

      </div>
      <div class="resultbox superres" :class="{ active: resultFlagPerf }">
        <p>Perfect result!</p>
        <p>Your average miss is 0-2°</p>
      </div>
    </div>
  </main>
</template>

<script>
import CitiesList from "./history.city.list.min.json";
export default {
  data() {
    return {
      cities: CitiesList,
      api_key: '0adb4facab58f7f719a5d9e4bd571b94',
      url: 'https://api.openweathermap.org/data/2.5/',
      random: 0,
      choosenFour: [],
      usersGuess: [],
      resultBox: false,
      resultFlagBad: false,
      resultFlagOk: false,
      resultFlagPerf: false,
    }
  },
  methods: {
    // search(e) {
    //   if (e.key == "Enter") {
    //     fetch(`${this.url}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
    //       .then(res => {
    //         return res.json();
    //       }).then(this.setResults);
    //   }
    // },

    changeCity() {
      this.resultBox = this.resultFlagBad = this.resultFlagOk = this.resultFlagPerf = false;
      this.choosenFour = [];
      this.usersGuess = [];
      for (let i = 0; i < 4; i++) {
        this.random = Math.round(Math.random() * 37208); //length of city list 
        this.query = this.cities[this.random].city.name;
        fetch(`${this.url}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
          .then(res => {return res.json()}).
            then(this.setResults);
      }
      console.log("4", this.choosenFour)
    },
    setResults(results) {
      this.choosenFour.push(results);
    },
    compareAnsw() {
      let x = 0;
      for (let i = 0; i < 4; i++) {
        if (this.usersGuess.length != 4 || this.usersGuess[i] === '') {
          alert("Fill the gape ")
          console.log("empty gape")
          return;  //breaks this fn
        } else if (this.usersGuess[i] === Math.round(this.choosenFour[i].main.temp)) {
          console.log('i:', i)
          //выделить угадывание
        } else if (this.usersGuess[i] > Math.round(this.choosenFour[i].main.temp)) {
          x += this.usersGuess[i] - Math.round(this.choosenFour[i].main.temp);
          console.log("inside x2", x)
        } else {
          x += (Math.round(this.choosenFour[i].main.temp)) - this.usersGuess[i];
          console.log("inside x3", x)
        }
      }
      if (x >= 20) {
        this.resultFlagBad = true;
        this.resultFlagOk = this.resultFlagPerf = false;
      } else if (20 > x && x > 8) {
        this.resultFlagOk = true;
        this.resultFlagBad = this.resultFlagPerf = false;
      } else {
        this.resultFlagPerf = true;
        this.resultFlagOk = this.resultFlagBad = false;
      }
      console.log('final x', x);
      this.resultBox = true;
    },

  }
}
</script>

<style  scoped>
main {
  position: absolute;
  top: 0;
  left: 0;
  padding: 0;
  margin: 0;
  background: linear-gradient(rgba(85, 190, 255, 0.5), rgba(255, 50, 252, 0.5));
  background-color: rgba(0, 0, 0, 0.23);
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.citiesGrid {
  border: 1px blue solid;
  height: 45vh;
  width: 70vw;
  display: grid;
  justify-content: center;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 1fr 0.16fr;
  padding: 10px;
}

.placeBox {
  border: 1px blue solid;
  margin: 10px;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
}

.btn {
  display: flex;
  width: 200%;
  justify-content: center;
}

.btn button {
  display: flex;
  align-self: center;
  cursor: pointer;
}

.results {
  margin-top: 1vh;
  display: inline-flex;
  border: 1px purple solid;
  width: 90%;
  height: 16vh;
  justify-content: space-around;
  align-items: center;
}

.resultbox {
  display: flex;
  flex-direction: column;
  border-radius: 10px;
  height: 10vh;
  width: 25%;
  justify-content: center;
  align-items: center;
  filter: grayscale(0.5)
}

.badres {
  background-color: rgb(255, 0, 0, 0.75);
}

.okres {
  background-color: rgb(255, 255, 0, 0.75);
}

.superres {
  background-color: rgb(17, 194, 46, 0.75);
}

.active {
  transition: 0.7s;
  filter: none;
  border: 5px black solid;
  transform: scale(1.5);
}
</style>