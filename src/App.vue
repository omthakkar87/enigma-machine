<template>
  <div id="app">
    <input type="text" onkeyup="if (/[^A-Z]/g.test(this.value)) this.value = this.value.replace(/[^A-Z]/g,'')" name="input" id="input" v-model="input" />
    <br />
    <br />
    <button @click="run()">Enigma</button>
    <br />
    <br />
    <!-- <h1>{{c3}}{{c2}}{{c1}}</h1> -->
    <the-mask :mask="mask" type="text" class="rotor" id="r3" maxlength="1" v-model="r3.counter" />
    <the-mask :mask="mask" type="text" class="rotor" id="r2" maxlength="1" v-model="r2.counter" />
    <the-mask :mask="mask" type="text" class="rotor" id="r1" maxlength="1" v-model="r1.counter" />
    <br />
    <p>{{output}}</p>
    <!-- <p>{{JSON.stringify(r1)}}</p> -->
    <!-- <table>
      <tr v-for="(c,i) in ir1" :key="i">
        <td>{{i}}-></td>
        <td>{{c}}</td>
        <td>{{ir2[i]}}</td>
        <td>{{ir3[i]}}</td>
      </tr>
    </table>-->
  </div>
</template>

<script>
import {TheMask} from 'vue-the-mask'
export default {
  components:{TheMask},
  name: "App",
  data() {
    return {
      input: "",
      output: "",
      rotors: [],
      mask:"A",
      lt: "ABCDEFGHIJKLMNOPQRSTUVWXYZ",
      r3: {
        counter: "A",
        wires: {
          A: "U",
          B: "Q",
          C: "N",
          D: "T",
          E: "L",
          F: "S",
          G: "Z",
          H: "F",
          I: "M",
          J: "R",
          K: "E",
          L: "H",
          M: "D",
          N: "P",
          O: "X",
          P: "K",
          Q: "I",
          R: "B",
          S: "V",
          T: "Y",
          U: "G",
          V: "J",
          W: "C",
          X: "W",
          Y: "O",
          Z: "A"
        },
        nextRotor: null,
        inverseWires: {
          U: "A",
          Q: "B",
          N: "C",
          T: "D",
          L: "E",
          S: "F",
          Z: "G",
          F: "H",
          M: "I",
          R: "J",
          E: "K",
          H: "L",
          D: "M",
          P: "N",
          X: "O",
          K: "P",
          I: "Q",
          B: "R",
          V: "S",
          Y: "T",
          G: "U",
          J: "V",
          C: "W",
          W: "X",
          O: "Y",
          A: "Z"
        }
      },
      r2: {
        counter: "A",
        wires: {
          A: "H",
          B: "Q",
          C: "Z",
          D: "G",
          E: "P",
          F: "J",
          G: "T",
          H: "M",
          I: "O",
          J: "B",
          K: "L",
          L: "N",
          M: "C",
          N: "I",
          O: "F",
          P: "D",
          Q: "Y",
          R: "A",
          S: "W",
          T: "V",
          U: "E",
          V: "U",
          W: "S",
          X: "R",
          Y: "K",
          Z: "X"
        },
        nextRotor: null,
        inverseWires: {
          H: "A",
          Q: "B",
          Z: "C",
          G: "D",
          P: "E",
          J: "F",
          T: "G",
          M: "H",
          O: "I",
          B: "J",
          L: "K",
          N: "L",
          C: "M",
          I: "N",
          F: "O",
          D: "P",
          Y: "Q",
          A: "R",
          W: "S",
          V: "T",
          E: "U",
          U: "V",
          S: "W",
          R: "X",
          K: "Y",
          X: "Z"
        }
      },
      r1: {
        counter: "A",
        wires: {
          A: "D",
          B: "M",
          C: "T",
          D: "W",
          E: "S",
          F: "I",
          G: "L",
          H: "R",
          I: "U",
          J: "Y",
          K: "Q",
          L: "N",
          M: "K",
          N: "F",
          O: "E",
          P: "J",
          Q: "C",
          R: "A",
          S: "Z",
          T: "B",
          U: "P",
          V: "G",
          W: "X",
          X: "O",
          Y: "H",
          Z: "V"
        },
        nextRotor: null,
        inverseWires: {
          D: "A",
          M: "B",
          T: "C",
          W: "D",
          S: "E",
          I: "F",
          L: "G",
          R: "H",
          U: "I",
          Y: "J",
          Q: "K",
          N: "L",
          K: "M",
          F: "N",
          E: "O",
          J: "P",
          C: "Q",
          A: "R",
          Z: "S",
          B: "T",
          P: "U",
          G: "V",
          X: "W",
          O: "X",
          H: "Y",
          V: "Z"
        }
      },
      rf: {
        A: "E",
        B: "J",
        C: "M",
        D: "Z",
        E: "A",
        F: "L",
        G: "Y",
        H: "X",
        I: "V",
        J: "B",
        K: "W",
        L: "F",
        M: "C",
        N: "R",
        O: "Q",
        P: "U",
        Q: "O",
        R: "N",
        S: "T",
        T: "S",
        U: "P",
        V: "I",
        W: "K",
        X: "H",
        Y: "G",
        Z: "D"
      }
    };
  },
  methods: {
    setWires(rotor, wiringTable) {
      var wires = {};
      var inverseWires = {};
      for (var i = 0; i < this.lt.length; i++) {
        wires[this.lt[i]] = wiringTable[i];
        inverseWires[wiringTable[i]] = this.lt[i];
      }
      rotor.wires = wires;
      rotor.inverseWires = inverseWires;
      return rotor;
    },
    updateInverse(rotor) {
      var inverseWires = {};
      for (var i = 0; i < this.lt.length; i++) {
        inverseWires[rotor.wires[this.lt[i]]] = this.lt[i];
      }
      rotor.inverseWires = inverseWires;
      return rotor;
    },
    rotate(rotor) {
      var newWires = {};
      var currentLetter, nextLetter;
      for (var i = 0; i < this.lt.length; i++) {
        currentLetter = this.lt[i];
        nextLetter = this.lt[(i + 1) % this.lt.length];
        newWires[currentLetter] = rotor.wires[nextLetter];
      }
      rotor.wires = newWires;
      return rotor;
    },
    step(rotor) {
      this.rotate(rotor);
      this.updateInverse(rotor);
      this.turnover(rotor);
      rotor.counter = String.fromCharCode((rotor.counter).charCodeAt() + 1);
      return rotor;
    },
    turnover(rotor) {
      if (rotor.counter == "Z") {
        rotor.counter = "A";
        if (rotor.nextRotor) {
          this.step(rotor.nextRotor);
        }
      }
      return rotor;
    },
    encodeRF(rf, letter) {
      return rf[letter];
    },
    encodeRT(rotor, letter, inverse) {
      if (inverse) {
        var letterCode = letter.charCodeAt() - "A".charCodeAt();
        var offset = (letterCode + (rotor.counter.charCodeAt() - "A".charCodeAt())) % this.lt.length;
        // console.log(letter,offset)
        if (offset < 0) {
          offset += 26;
        }
        return rotor.inverseWires[
          String.fromCharCode("A".charCodeAt() + offset)
        ];
      } else {
        var inverseCode = rotor.wires[letter].charCodeAt() - "A".charCodeAt();

        var ioffset = (inverseCode - (rotor.counter.charCodeAt() - "A".charCodeAt())) % this.lt.length;
        if (ioffset < 0) {
          ioffset += 26;
        }
        return String.fromCharCode("A".charCodeAt() + ioffset);
      }
    },
    enigma(rotors, reflector, letter) {
      var encodedletter = letter;
      this.step(rotors[0]);
      for (var i = 0; i < rotors.length; i++) {
        encodedletter = this.encodeRT(rotors[i], encodedletter);
      }

      encodedletter = this.encodeRF(reflector, encodedletter);

      for (var j = rotors.length - 1; j >= 0; j--) {
        encodedletter = this.encodeRT(rotors[j], encodedletter, 1);
      }
      this.output += encodedletter;
      return rotors, reflector, letter;
    },
    run() {
      this.output = "";
      for (var i = 0; i < this.input.length; i++) {
        this.enigma(this.rotors, this.rf, this.input[i]);
      }
    }
  },
  mounted() {
    this.rotors = [this.r1, this.r2, this.r3];
    this.rotors[0].nextRotor = this.rotors[1];
    this.rotors[1].nextRotor = this.rotors[2];
  }
};
</script>
<style>
* {
  font-family: "Fira Code", Courier, monospace;
  word-wrap: break-word;
  font-size: 30pt;
}
body,
html {
  padding: 0.7em;
  text-align: center;
}
input {
  width: 100%;
  text-align: center;
}
.rotor {
  width: 20%;
  margin: 0.2em;
}
</style>