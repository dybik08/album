<template>
  <div :class="[{ flexStart: step === 1 } ,'wrapper']">
    <transition name="slide">
      <img id="img" src="./assets/odyssey.svg" class="logo" v-if="step === 1" v-on:click="handleClick" >
    </transition>
    <transition name="fade">
      <BackgroundImage v-if="step === 0"/>
    </transition>
    <Claim v-if="step === 0" />
    <SearchInput :value="searchValue" v-model="searchValue" @input="handleInput" :dark="step === 1" />
    <div class="results" v-if="results && !loading && step === 1">
      <a v-for="item in results"  data-fancybox="gallery" v-bind:data-caption="item.data[0].title" v-bind:href="item.links[0].href" ><img id="img-list" v-bind:src="item.links[0].href"></a>
    </div>
    <div class="loader" v-if="step === 1 && loading" />
  </div>
</template>
<script>
    import axios from 'axios';
    import debounce from 'lodash.debounce';
    import Claim from '@/components/Claim.vue';
    import SearchInput from '@/components/SearchInput.vue';
    import BackgroundImage from "./components/BackgroundImage";
    import Item from "./components/Item";

    const API = 'https://images-api.nasa.gov/search';
    export default {
        name: 'App',
        components: {
            BackgroundImage,
            Claim,
            SearchInput,
            Item,
        },
        data() {
            return {
                loading: false,
                step: 0,
                searchValue: '',
                results: [],
            };
        },
        methods: {
            handleClick() {
                this.searchValue = '';
                this.step = 0;
                this.loading = false;

            },
            handleInput: debounce(function() {
                this.loading = true;
                axios.get(`${API}?q=${this.searchValue}&media_type=image`)
                    .then((response) => {
                        this.results = response.data.collection.items;
                        this.loading = false;
                        this.step = 1;
                    })
                    .catch((error) => {
                        console.log(error);
                    });
            }, 1000),
        },
    };
</script>
<style lang="scss">
  @import url('https://fonts.googleapis.com/css?family=Montserrat:300,400,600,800');
  * {
    box-sizing: border-box;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }
  body {
    font-family: 'Montserrat', sans-serif;
    margin: 0;
    padding: 0;
  }
  .fade-enter-active, .fade-leave-active {
    transition: opacity 1.9s ease;
    transition-delay: 0.5s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
  .slide-enter-active {
      transition: margin-top .7s ease;
      transition-delay: 0.1s;

  }
  .slide-enter {
      margin-top: -250px;
  }
  .slide-leave-active {
      transition: margin-top .7s ease;

  }
  .slide-leave-to {
      margin-top: -450px;
  }



  .wrapper {
    margin: 0;
    width: 100%;
    position: relative;
    min-height: 100vh;
    padding: 30px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    &.flexStart {
      justify-content: flex-start;
      background-color: black;
    }
  }
  .logo {
    position: absolute;
    height: 160px;
  }
  .results {
      margin-top: 50px;
      display: grid;
      grid-template-columns: 1fr ;
      grid-gap: 20px;
      @media (min-width: 650px) {
          width: 100%;
          grid-template-columns: 1fr 1fr;
      }
      @media (min-width: 992px) {
          width: 90%;
          grid-template-columns: 1fr 1fr 1fr;
      }
  }
  .loader {
    margin-top: 100px;
    display: inline-block;
    width: 64px;
    height: 64px;
    @media (min-width: 768px) {
      width: 90px;
      height: 90px;
    }
  }
  .loader:after {
    content: " ";
    display: block;
    width: 46px;
    height: 46px;
    margin: 1px;
    border-radius: 50%;
    border: 5px solid #1e3d4a;
    border-color: #1e3d4a transparent #1e3d4a transparent;
    animation: loading 1.2s linear infinite;
    @media (min-width: 768px) {
      width: 90px;
      height: 90px;
    }
  }
  @keyframes loading {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }
  a img {
    width: 100%;
    height: 350px;
    transition: transform .2s;
  }
  #img-list:hover{
      transform: scale(1.05);
      border-style: solid ;
      border-width: 1px;
      border-color: white;
  }
</style>