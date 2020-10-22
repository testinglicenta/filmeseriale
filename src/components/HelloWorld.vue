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
        <div class="header-table col-flex-center">
            <div class="header-key header-title"  @click="sortBy('title')">Title</div>
            <div class="header-key header-year"  @click="sortBy('year')">Year</div>
            <div class="header-key header-status"  @click="sortBy('status')">Status</div>
            <div class="header-key header-rating"  @click="sortBy('rating')">Rating</div>
            <div class="header-key header-link"  @click="sortBy('link')">Link</div>
            <div class="header-key header-description"  @click="sortBy('description')">Description</div>
        </div>
        <div v-for="item in list" :key="item" class="content-wrapper col-flex-center">
            <div class="content-key content-title"  @click="sortBy('title')"><span>{{ item.title }}</span></div>
            <div class="content-key content-year"  @click="sortBy('year')"><span>{{ item.year }}</span></div>
            <div class="content-key content-status"  @click="sortBy('status')"><span>{{ item.status }}</span></div>
            <div class="content-key content-rating"  @click="sortBy('rating')"><span>{{ item.rating }}</span></div>
            <div class="content-key content-link"  @click="sortBy('link')"><span>{{ item.link }}</span></div>
            <div class="content-key content-description"  @click="sortBy('description')"><span>{{ item.description }}</span></div>
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
      list: [],
      year: false,
      rating: false,
    }
  },
  methods: {
    selectedFile(event){
      const fileList = event.target.files;
      console.log(fileList);

      var input = event.target;
      var reader = new FileReader();
      reader.onload = ()=>{
        var text = reader.result;
        // var node = document.getElementById('output');
        // node.innerText = text;
        // console.log(JSON.parse(reader.result));
        this.list = JSON.parse(reader.result);
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
    },
    sortBy(key){
      console.log('sortBy', key);
      switch (key) {
        case 'year':
          this.year = !this.year;
          if(this.year){
            this.list.sort((a, b) => {
              var keyA = Number(a.year),
                keyB = Number(b.year);
              if (keyA < keyB) return -1;
              if (keyA > keyB) return 1;
              return 0;
            });
          }else{
            this.list.sort((a, b) => {
              var keyA = Number(a.year),
                keyB = Number(b.year);
              if (keyA > keyB) return -1;
              if (keyA < keyB) return 1;
              return 0;
            });
          }
          break;
        case 'rating':
          this.rating = !this.rating;
          if(this.rating){
            this.list.sort((a, b) => {
              var keyA = Number(a.rating),
                keyB = Number(b.rating);
              if (keyA < keyB) return -1;
              if (keyA > keyB) return 1;
              return 0;
            });
          }else{
            this.list.sort((a, b) => {
              var keyA = Number(a.rating),
                keyB = Number(b.rating);
              if (keyA > keyB) return -1;
              if (keyA < keyB) return 1;
              return 0;
            });
          }
          break;
      
      }
    }
  }
}
</script>
