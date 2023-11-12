<template>
  <form autocomplete="off">
    <div class="form-wrapper">
      <div class="input-wrapper">
        <input @focus="focusInput" v-model="input" type="text" placeholder="Введите логин..." />
        <div v-if="load" class="loader"></div>
        <div v-if="input.length > 1" :class="[{'disable-icon': !this.showList}, 'down-icon']" @click="openList">
          <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path fill-rule="evenodd" clip-rule="evenodd" d="M18.3536 15.3536C18.1583 15.5488 17.8417 15.5488 17.6464 15.3536L12 9.70711L6.35355 15.3536C6.15829 15.5488 5.84171 15.5488 5.64645 15.3536C5.45118 15.1583 5.45118 14.8417 5.64645 14.6464L11.6464 8.64645C11.8417 8.45118 12.1583 8.45118 12.3536 8.64645L18.3536 14.6464C18.5488 14.8417 18.5488 15.1583 18.3536 15.3536Z" fill="#262626"/>
          </svg>
        </div>
      </div>
      <div v-if="persons?.length && showList" class="list-wrapper">
        <ul class="list" :key="person" v-for="person in persons">
          <li @click="selectPerson(person.login)" class="list-items">{{"@" + person?.login}}</li>
        </ul>
      </div>
    </div>
  </form>

</template>

<script>
export default {
  name: "SelectPerson",
  data() {
    return {
      selected: "",
      input: "",
      persons: [],
      load: false,
      showList: false,
    };
  },
  methods: {
    focusInput(){
      if(!this.input) this.input = "@"
    },
    openList(){
      if(this.persons?.length)
        this.showList = !this.showList
    },
    selectPerson(value) {
      this.input = "@" + value;
      this.showList = false;
    },
    findPerson() {
      if(this.input.length <= 1) return;
      this.persons = [];
      this.load = true;
      setTimeout(() => this.getPerson(this.input.toLowerCase().slice(1), this.persons), 1000);
    },
    getPerson(value, persons) {
      this.load = false;
      fetch(`https://api.github.com/search/users?q=${value}`)
        .then((response) => {
          return response.json();
        })
        .then((data) => {
          data?.items
            ?.filter((e) => e.login.startsWith(value))
            .sort()
            .forEach((e) => persons.push(e))
        });
    },
  },
  watch: {
    input: {
      handler() {
        this.findPerson();
      },
    },
  }
}
</script>

<style lang="sass" scoped>
form 
  width: 75vmin
  height: 100%
  position: absolute
  transform: translate(-50%, -50%)
  top: 50%
  left: 50%
  padding: 40px 0

input
  width: 100%
  padding: 4px
  border: none
  border-radius: 3px
  outline: none
  background-color: #fff
  font-size: 16px
  font-style: normal
  font-weight: 400
  line-height: 20px
  color: #262626
  
ul 
  list-style: none

.input-wrapper
  width: 260px
  display: flex
  position: relative
  $border: 1px
  border-radius: 0.25em
  &:before
    content: ''
    position: absolute
    top: 0
    right: 0
    bottom: 0
    left: 0
    z-index: -1
    margin: -$border 
    border-radius: inherit 
    background: linear-gradient(90.87deg, #FFC700 0%, #A5E870 100%)

.form-wrapper
  max-height: 156px
  flex-direction: column
  width: 264px
  display: flex
  align-items: center
  position: relative
  padding: 1px
  overflow: hidden
  $border: 1px
  background-clip: padding-box 
  border: solid $border transparent 
  border-radius: 0.25em
  &:before
    content: ''
    position: absolute
    top: 0
    right: 0
    bottom: 0
    left: 0
    z-index: -1
    margin: -1px
    border-radius: inherit
    background: linear-gradient(90.87deg, #FFC700 0%, #A5E870 100%)

.list-wrapper
  max-height: 124px
  flex-direction: column
  width: 260px
  display: flex
  margin-top: 1px
  overflow-y: auto
  border-bottom-left-radius: 2px
  border-bottom-right-radius: 2px
  &::-webkit-scrollbar
    width: 0

.list
  width: 100%
  background-color: #fff
  padding: 0

.list-items 
  height: 24px
  display: flex
  align-items: center
  padding: 0px 7px
  cursor: pointer
  
.list-items:hover 
  background-color: rgba(255, 201, 4, 0.17)

.loader
  width: 16px
  height: 16px
  border-radius: 50%
  display: inline-block
  position: absolute
  right: 32px
  top: 6px
  background: linear-gradient(0deg, rgba(255, 61, 0, 0.2) 33%, #FFC700 100%)
  box-sizing: border-box
  animation: rotation 1s linear infinite

.loader::after 
  content: ''
  box-sizing: border-box
  position: absolute
  left: 50%
  top: 50%
  transform: translate(-50%, -50%)
  width: 12px
  height: 12px
  border-radius: 50%
  background: var(--grey-0, #FFF)

@keyframes rotation 
  0%
    transform: rotate(0deg)
  100%
    transform: rotate(360deg)
  
.disable-icon
  cursor: pointer
  transform: rotate(180deg)

.down-icon
  position: absolute
  cursor: pointer
  right: 4px
  top: 2px
  width: 24px
  height: 24px
</style>