<template>
  <v-app>
    <v-row>
      <v-col cols="4"  class="black" style="overflow-y:scroll; height:100vh">
        <v-row v-for="(img, ix) in imageSrcArr" v-bind:key="ix">
          <v-spacer></v-spacer>
          <v-col>
            <v-btn
              @click="setSelectedImage(ix)"
              v-if="selectedImg != imageSrcArr[ix]"
              style="width: 80%"
              class=""
            >
              Image {{ ix }}
            </v-btn>
            <v-btn
              class="red"
              v-if="selectedImg == imageSrcArr[ix]"
              style="width: 80%"
            >
              Image {{ ix }}
            </v-btn> </v-col
          ><v-spacer></v-spacer>
        </v-row>
      </v-col>
      <v-col cols="8" class="red">
        <v-row><v-toolbar><v-toolbar-title class="mx-auto">{{selectedImg.photographer==null?"No photographer":selectedImg.photographer}}</v-toolbar-title></v-toolbar></v-row>
        <v-row>
          <v-spacer></v-spacer>
          <v-col cols="8"
            ><v-img
              sizes="200px"
              width="100%"
              height="50vh"
              :src="selectedImg.src.medium"
            ></v-img
          ></v-col>
          <v-spacer></v-spacer>
        </v-row>
        <v-row>
          <v-col cols="4" v-for="ix in 3" v-bind:key="ix"
            ><v-img
            style="cursor: pointer;"
              @click="setSelectedImage(parseInt(selectedImg.ix) + ix)"
              contain
              sizes="200px"
              width="100%"
              height="25vh"
              :src="getSelectedImage(parseInt(selectedImg.ix) + ix).src.medium"
            ></v-img
          ></v-col>
        </v-row>
      </v-col>
    </v-row>
  </v-app>
</template>

<script>
import axios from "axios";
export default {
  name: "App",

  components: {},

  data: () => ({
    //

    authToken: "563492ad6f917000010000017f488949f5c24f7cb9fc4ad4069c1050",
    imageUrl: "https://api.pexels.com/v1/search",
    imageSrcArr: [],
    searchTopic: "nature",
    selectedImg: "",
  }),

  beforeMount() {
    this.retrieveImages();
  },

  methods: {
    retrieveImages() {
      this.imageArr = [];
      axios
        .get(this.imageUrl + "?query=" + this.searchTopic, {
          headers: { Authorization: this.authToken },
        })
        .then((response) => {
          console.log(response.data.photos);
          this.imageSrcArr = response.data.photos;
          this.selectedImg = this.imageSrcArr[0];
          this.selectedImg.ix = 0;
          console.log(this.imageSrcArr);
        })
        .catch((error) => {
          console.log(error);
          this.errored = true;
        })
        .finally(() => (this.loading = false));
    },


    getSelectedImage(ix)
    {
     // console.log(ix)
      if (ix>=this.imageSrcArr.length) // when index of requested img is above list lenght
      { var delta = ix - this.imageSrcArr.length //
      console.log(delta)
        return this.imageSrcArr[delta]
      }
      else 
      return this.imageSrcArr[ix]
    },

    setSelectedImage(ix) {
      if (ix>=this.imageSrcArr.length) // when index of requested img is above list lenght
      { var delta = ix - this.imageSrcArr.length //
      console.log(delta)
      this.selectedImg = this.imageSrcArr[delta];
      this.selectedImg.ix = delta; 
      }
      else {
      this.selectedImg = this.imageSrcArr[ix];
      this.selectedImg.ix = ix; // to be used for retrieving current image index referencing parent list
      }
    },
  },
};
</script>
