<template>
  <div class="generator page" id="generator">
    <div class="generator-form container">
      <div class="new" @click="payoff = newPayoff()">v2<span>{{payoff}}</span></div>
      <h2 class="generator-heading"><span>Recruitment Ipsum is a collection of real recruitment mails.</span> <span>They're finally getting useful as placeholder text!</span></h2>
      <form v-on:submit.prevent="generateRecruitmentIpsum">
        <div class="columns">
          <div class="column first">
            <div class="form-input"><input v-model="amount" type="text" name="amount" maxlength="3" min="1" max="999"></div>
          </div>
          <div class="column">
            <div class="form-radio"><input v-model="type" type="radio" name="type" id="typeParagraphs" value="sentences"><label for="typeParagraphs"><span>paragraphs</span> <i class="fas fa-angle-right"></i></label></div>
            <div class="form-radio"><input v-model="type" type="radio" name="type" id="typeLists" value="listitems"><label for="typeLists"><span>lists</span> <i class="fas fa-angle-right"></i></label></div>
          </div>
          <div class="column">
            <div class="form-radio"><input v-model="language" type="radio" name="language" id="languageEN" value="en"><label for="languageEN"><span>english</span> <i class="fas fa-angle-right"></i></label></div>
            <div class="form-radio"><input v-model="language" type="radio" name="language" id="languageNL" value="nl"><label for="languageNL"><span>dutch</span> <i class="fas fa-angle-right"></i></label></div>
          </div>
          <div class="column last">
            <button type="submit">generate <i class="far fa-thumbs-up"></i></button>
          </div>
          <div class="startwith">
            <input v-model="startwith" type="checkbox" name="startwith" id="startWith" value="true"><label for="startWith"><div class="checkbox"><i class="fas fa-check" v-if="startwith"></i></div> <span>Start with "Recruiment Ipsum ..."</span></label>
          </div>
        </div>
      </form>
    </div>
    <div id="result">
        <div class="container generated" v-if="ipsum">
          <h2>Here is your Recruitment Ipsum!</h2>
          <p v-for="(content, index) in ipsum.content" v-bind:key="index" v-if="ipsum.type === 'sentences'">
            {{content}}
          </p>
          <ul v-for="(content, index) in ipsum.content" v-bind:key="index" v-if="ipsum.type === 'listitems'">
            <li v-for="(group, subindex) in content.items" v-bind:key="subindex">
              {{group.item}}
            </li>
          </ul>
        </div>
    </div>
  </div>
</template>

<script>
export default {
  data: function () {
    return {
      json: require('../assets/content.json'),
      ipsum: null,
      type: 'sentences',
      language: 'en',
      amount: 15,
      startwith: true,
      payoff: null
    }
  },
  created: function() {
    this.payoff = this.json.payoffs[0];
  },
  methods: {
    newPayoff: function() {
      let index;
      let newPayoff = this.payoff;
      
      while(newPayoff === this.payoff) {
        let index = Math.floor(Math.random() * this.json.payoffs.length);
        newPayoff = this.json.payoffs[index];
      }

      return newPayoff;
    },
    generateRecruitmentIpsum: function () {
      let result = { content: [], type: this.type };
      let data;
      let items = [];
      let randomCount;
      let tempDocument;

      // get content from the json file
      data = this.json[this.language][this.type];

      // create an array with random data that's long enough
      while(items.length < this.amount * 8) {
        items = items.concat(data.sort(function () { return 0.5 - Math.random() }));
      }

      // add start sentence when requested
      if(this.startwith) {
        this.type === 'sentences' ? items.unshift(this.json[this.language].startSentence) : items.unshift(this.json[this.language].startListitem);
      }

      // generate that awesome recruitment ipsum
      for (let i = 0; i < this.amount; i++) {
        randomCount = Math.floor((Math.random() * 3) + 4);
        result.content[i] = this.type === 'sentences' ? '' : { items: [] };
        for(let j=0; j < randomCount; j++) {
          // build paragraphs
          if(this.type === 'sentences') {
            result.content[i] += items[0] + ' ';
          // build list items
          } else {
            result.content[i].items.push({item: items[0]});
          }
          items.shift();

          // every paragraphs should be at least 350 charachters long 
          if(j === randomCount-1 && result.content[i].length < 350 && this.type === 'sentences') {
            j--;
          }
          // stop loop when a paragraph is longer than 400 characters
          if(result.content[i].length > 400 && this.type === 'sentences') {
            j = randomCount;
          }

        }
      }

      // set new lorem ipsum to display
      this.ipsum = result;

      // scroll to result container
      var VueScrollTo = require('vue-scrollto');
      VueScrollTo.scrollTo('#result', 500, {easing: 'ease-out', offset: -60, x: false, y: true });
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
  .generator {

    .generator-form {
      @media (min-width: 900px) {
        margin-bottom: 30px;
      }
    }

    .new {
      background: #fae596;
      cursor: pointer;
      display: none;
      font-weight: bold;
      font-size: 3.4em;
      text-align: center;
      padding-top: 10px;
      line-height: 1;
      width: 120px;
      height: 120px;
      border-radius: 50%;
      position: absolute;
      transform: rotate(30deg);
      user-select: none;
      right: 20px;
      top: -150px;

      @media (min-width: 900px) {
        display: block;
      }

      @media (min-width: 1000px) {
        right: -40px;
        top: -40px;
      }

      span {
        display: block;
        font-size: 14px;
        line-height: 1.2;
        margin: 0 auto;
        max-width: 90%;
      }
    }

    .columns {
      align-items: center;
      display: flex;
      flex-direction: column;
      flex-wrap: wrap;
      font-size: 18px;
      margin: 0 auto;

      @media (min-width: 900px) {
        flex-direction: row;
        width: 90%;
      }

      .column {
        margin-bottom: 20px;
        order: 1;
        width: 100%;

        @media (max-width: 899px) {
          display: flex;
          justify-content: space-between;

          &.last,
          &.first {
            justify-content: center;
          }

          &.last {
            order: 3;
          }
        }
        
        @media (min-width: 900px) {
          padding: 0 20px;
          width: 30%;

          &.last,
          &.first {
            width: 20%;
          }

          &.last {
            order: 2;
          }
        }
      }
    }

    .form-input {
      text-align: right;

      input {
        border: 1px solid #173e43;
        height: 40px;
        font-size: 18px;
        text-align: center;
        width: 60px;

        @media (min-width: 900px) {
          height: 60px;
          width: 80px;
        }
      }
    }

    .startwith {
      font-size: 0.8em;
      margin-bottom: 20px;
      order: 2;
      width: 100%;

      @media (min-width: 900px) {
        font-size: 1em;
        margin-bottom: 0;
        order: 3;
        text-align: center;
      }
      
      input {
        display: none;
      }

      label {
        align-items: center;
        cursor: pointer;
        display: flex;
        justify-content: center;
      }

      .checkbox {
        align-items: center;
        border: 1px solid #173e43;
        display: flex;
        height: 20px;
        justify-content: center;
        font-size: 0.8em;
        margin-right: 10px;
        width: 20px;

        @media (min-width: 900px) {
          height: 30px;
          width: 30px;
        }
      }
    }

    .form-radio {
      display: inline-block;
      width:47%;

      @media (min-width: 900px) {
        width: 100%;
      }

      input {
        display: none;
      }

      i {
        display: none;
      }

      input:checked + label {
        background: #173e43;
        color: white;
        position: relative;
        
        i { 
          display: inline-block;
        }
      }

      label {
        align-items: center;
        border: 1px solid #173e43;
        cursor: pointer;
        display: flex;
        font-size: 0.8em;
        height: 40px;
        justify-content: space-between;
        padding: 0 10px;
        width: 100%;

        @media (min-width: 900px) {
          font-size: 1em;
          height: 60px;
          margin-bottom: 20px;
          padding: 0 20px;
        }
      }
    }

    button {
      background-color: #3fb0ac;
      border: 1px solid darken(#3fb0ac, 8);
      color: white;
      cursor: pointer;
      font-size: 18px;
      font-weight: bold;
      height: 40px;
      text-align: center;
      width: 140px;

      @media (min-width: 900px) {
        height: 60px;
      }

      &:active {
        background-color: white;
        border: 1px solid #3fb0ac;
        color: #3fb0ac;
      }
    }

    h2 {
      font-size: 1.2em;
      line-height: 1.6;
      margin: 0 0 1em;

      @media (min-width: 900px) {
        font-size: 1.6em;
      }
    }

    .generator-heading {
      font-size: 1em;
      font-weight: normal;
      margin: 0 0 2em;
      text-align: center;

      @media (min-width: 900px) {
        font-size: 1.5em;
        font-weight: bold;

        span {
          display: block;
        }
      }
    }
  }
</style>
