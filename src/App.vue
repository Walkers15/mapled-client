<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <div>
    <h1>Maple Diary</h1>
    <p>한번 등록하고 나면 매일 자동으로 캐릭터의 정보를 갱신해줍니다.</p>
    <p>나날이 강해져가는 내 캐릭터를 확인하세요!</p>
  </div>
  <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
  <div>
    <div v-if="showSearchBlock === true">
      캐릭터 닉네임을 입력하세요:
      <input v-model="nickname" />
      <p>{{ nickname }}</p>
      <button @click="search">검색하기</button>
      <p>{{ searchMessage }}</p>
      <button v-if="registerButton === true" @click="register">등록하기</button>
    </div>
    <div v-if="findCharacterData === true">
      <p>{{ nickname }} 님의 다이어리</p>
      <table border="1" style="margin-left: auto; margin-right: auto">
        <th>등록일자</th>
        <th>레벨</th>
        <th>경험치</th>
        <th>메소</th>
        <th>스텟 공격력</th>
        <th>보스 공격력</th>
        <th>STR</th>
        <th>DEX</th>
        <th>LUK</th>
        <th>INT</th>
        <tr v-for="characterInfo in characterData" :key="characterInfo">
          <td>{{ characterInfo.createdAt }}</td>
          <td>{{ characterInfo.level }}</td>
          <td>{{ characterInfo.exp.toLocaleString() }}</td>
          <td>{{ characterInfo.meso.toLocaleString() }}</td>
          <td>{{ characterInfo.statAttack.toLocaleString() }}</td>
          <td>{{ characterInfo.bossAttack }}</td>
          <td>{{ characterInfo.str.toLocaleString() }}</td>
          <td>{{ characterInfo.dex.toLocaleString() }}</td>
          <td>{{ characterInfo.int.toLocaleString() }}</td>
          <td>{{ characterInfo.luk.toLocaleString() }}</td>
        </tr>
      </table>
      <br />
      <button @click="reset">뒤로가기</button>
    </div>
  </div>
</template>

<script>
// import HelloWorld from "./components/HelloWorld.vue";
import axios from "axios";

export default {
  name: "App",
  components: {
    // HelloWorld,
  },
  data() {
    return {
      nickname: "",
      searchMessage: "",
      registerButton: false,
      findCharacterData: false,
      characterData: [],
      showSearchBlock: true,
    };
  },
  methods: {
    async search() {
      try {
        const result = await axios.get(`http://192.168.0.118:3030/search/${this.nickname}`);
        if (result.data.isRegister === false) {
          this.searchMessage = result.data.message;
          this.registerButton = true;
        } else {
          console.log(result.data);
          this.showSearchBlock = false;
          this.findCharacterData = true;
          this.characterData = result.data.characterData;
        }
      } catch {
        this.searchMessage = "찾는 캐릭터가 없습니다";
      }
    },
    async register() {
      try {
        const result = await axios.post(`http://192.168.0.118:3030/register`, { nickname: this.nickname });
        console.log(result.data);
        alert("등록을 완료했습니다!");
        this.showSearchBlock = false;
        this.findCharacterData = true;
        this.characterData = result.data.characterData;
        this.registerButton = false;
      } catch {
        alert("문제가 발생했습니다");
      }
    },
    reset() {
      this.nickname = "";
      this.characterData = [];
      this.findCharacterData = false;
      this.searchMessage = "";
      this.showSearchBlock = true;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
