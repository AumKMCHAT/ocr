<template>
  <div class="container">
    <div class="upload">
      <form action="/index.html" method="post" enctype="multipart/form">
        <v-file-input label="File input" filled prepend-icon="mdi-camera" @change="onFileSelected"/>
        <v-btn depressed color="primary" @click="onSubmitFile"> Submit </v-btn>
      </form>
    </div>
  </div>
</template>