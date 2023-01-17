<template>
  <v-app style="height: 100%">
    <v-row style="height: 5vh"></v-row>
    <v-row style="height: 90vh">
      <v-col cols="4" class="black" style="overflow-y: scroll; height: 100%">
        <v-row v-for="(img, ix) in imageSrcArr" v-bind:key="ix">
          <v-spacer></v-spacer>
          <v-col>
            <v-btn
              @click="setSelectedImage(ix)"
              v-if="selectedImg != imageSrcArr[ix]"
              style="width: 80%"
              class=""
            >
              Image {{ ix + 1 }}
            </v-btn>
            <v-btn class="red" v-if="selectedImg == imageSrcArr[ix]" style="width: 80%">
              Image {{ ix + 1 }}
            </v-btn> </v-col
          ><v-spacer></v-spacer>
        </v-row>
      </v-col>
      <v-col cols="8" class="grey">
        <v-row
          ><v-toolbar
            ><v-toolbar-title
              class="mx-auto clickable"
              @click="goTo(selectedImg.photographer_url)"
              >{{
                selectedImg.photographer == null
                  ? "No photographer"
                  : selectedImg.photographer
              }}</v-toolbar-title
            ></v-toolbar
          ></v-row
        >
        <v-row>
          <v-spacer></v-spacer
          ><v-col cols="8">
            <v-text-field
              color="white"
              append-icon="mdi-magnify"
              label="Search topic"
              :placeholder="searchTopic"
              v-model="searchTopic"
              outlined
              dense
              clearable
              @click:append="retrieveImages"
              @keydown.enter="retrieveImages"
            ></v-text-field></v-col
          ><v-spacer></v-spacer
        ></v-row>
        <v-row>
          <v-spacer></v-spacer>
          <v-col cols="8"
            ><v-img
              contain
              sizes="200px"
              width="100%"
              height="40vh"
              :src="selectedImg.src.medium != null ? selectedImg.src.medium : ''"
            ></v-img
          ></v-col>
          <v-spacer></v-spacer>
        </v-row>
        <v-row>
          <v-col cols="4" v-for="ix in 3" v-bind:key="ix"
            ><v-img
              :class="ix == 1 ? 'clickable red-borders' : 'clickable'"
              @click="setSelectedImage(parseInt(selectedImg.ix) + ix - 1)"
              sizes="200px"
              width="100%"
              height="20vh"
              :src="getSelectedImage(parseInt(selectedImg.ix) + ix - 1).src.medium"
            ></v-img
          ></v-col>
        </v-row>
      </v-col>
    </v-row>
    <v-row style="height: 5vh"></v-row>
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
    /**
     * This method retrieves images from backend as links. They will be displayed in image tags
     */
    retrieveImages() {
      this.imageArr = [];
      axios
        .get(this.imageUrl + "?query=" + this.searchTopic, {
          headers: { Authorization: this.authToken },
        })
        .then((response) => {
          //console.log(response.data.photos);
          this.imageSrcArr = response.data.photos;
          this.selectedImg = this.imageSrcArr[0];
          this.selectedImg.ix = 0;
          //console.log(this.imageSrcArr);
        })
        .catch((error) => {
          console.log(error);
          this.errored = true;
        })
        .finally(() => (this.loading = false));
    },
    /**
     * This method handles indexes to get the correct image from cached list
     */
    getSelectedImage(ix) {
      // console.log(ix)
      if (ix >= this.imageSrcArr.length) {
        // when index of requested img is above list lenght
        var delta = ix - this.imageSrcArr.length; //
        console.log(delta);
        return this.imageSrcArr[delta];
      } else return this.imageSrcArr[ix];
    },
    // Just a window open with target blank. 
    goTo(url) {
      window.open(url, "_blank").focus();
    },

    /**
     * This method handles indexes to get the correct image from cached list
     */
    setSelectedImage(ix) {
      if (ix >= this.imageSrcArr.length) {
        // when index of requested img is above list lenght
        var delta = ix - this.imageSrcArr.length; //
        //console.log(delta);
        this.selectedImg = this.imageSrcArr[delta];
        this.selectedImg.ix = delta;
      } else {
        this.selectedImg = this.imageSrcArr[ix];
        this.selectedImg.ix = ix; // to be used for retrieving current image index referencing parent list
      }
    },
  },
};
</script>

<style scoped>
.clickable {
  cursor: pointer;
}
.red-borders {
  border: 2px solid;
  border-color: red;
}
</style>
