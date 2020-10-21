<template>
  <div>
    <h1>{{ msg }}</h1>
    <button @click="count++">count is: {{ count }}</button>
    <p>Edit <code>components/HelloWorld.vue</code> to test hot module replacement.</p>

    <div class="col-flex-center">
      <button class="btn-films" @click="onClickBtn('films')">
        Get Films
      </button>
      <button class="btn-serials" @click="onClickBtn('serials')">
        Get Serials
      </button>
    </div>

    <input type="file" id="file-selector" multiple @change="selectedFile($event)">
    <div id='output'>
    </div>
    <div class="table-wrapper">
        <div v-for="item in list" :key="item">
            {{ item }}
        </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      count: 0,
      list: ['a','c','d'],
    }
  },
  methods: {
// title
// year
// status
// rating
// link
// description
    selectedFile(event){
      const fileList = event.target.files;
      console.log(fileList);

      var input = event.target;
      var reader = new FileReader();
      reader.onload = function(){
        var text = reader.result;
        var node = document.getElementById('output');
        node.innerText = text;
      console.log(JSON.parse(reader.result));
        // console.log(reader.result.substring(0, 200));
      };
      reader.readAsText(input.files[0]);
    },
    onClickBtn(text){
      if(text == 'films'){
        console.log('text', text);
      }else{
        console.log('text', text);
      }
    }
  }
}
</script>
