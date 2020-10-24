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
            <div class="header-key header-title"  @click="sortBy('title')">
              <span class="header-year-title" @click="sortBy('title')">Title</span>
              <input type="text" placeholder="search" @keyup="filterInput($event, 'title')">
            </div>
            <div class="header-key header-year">
              <span class="header-year-text" @click="sortBy('year')">Year</span>
              <input type="text" placeholder="search" @keyup="filterInput($event, 'year')">
            </div>
            <div class="header-key header-status"  @click="sortBy('status')">Status</div>
            <div class="header-key header-rating">
              <span class="header-rating-text" @click="sortBy('rating')">Rating</span>
              <input type="text" placeholder="search" @keyup="filterInput($event, 'rating')">
            </div>
            <div class="header-key header-link"  @click="sortBy('link')">Link</div>
            <div class="header-key header-description"  @click="sortBy('description')">Description</div>
        </div>
        <div v-for="item in filteredList" :key="item" class="content-wrapper col-flex-center">
            <div class="content-key content-title"  @click="sortBy('title')"><span>{{ item.title }}</span></div>
            <div class="content-key content-year"  @click="sortBy('year')"><span>{{ item.year }}</span></div>
            <div class="content-key content-status"  @click="sortBy('status')"><span>{{ item.status }}</span></div>
            <div class="content-key content-rating"  @click="sortBy('rating')"><span>{{ item.rating }}</span></div>
            <div class="content-key content-link"  @click="sortBy('link')"><a :href="item.link" target="_blank">{{ item.link }}</a></div>
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
      filteredList: [],
      inputTitle: '',
      inputYear: '',
      inputRating: '',
      sortYear: false,
      sortRating: false,
      timeout: null,
      timeoutDelay: 500,
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
        this.filteredList = this.list;
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
          this.sortYear = !this.sortYear;
          if(this.sortYear){
            this.filteredList.sort((a, b) => {
              var keyA = Number(a.year),
                keyB = Number(b.year);
              if (keyA < keyB) return -1;
              if (keyA > keyB) return 1;
              return 0;
            });
          }else{
            this.filteredList.sort((a, b) => {
              var keyA = Number(a.year),
                keyB = Number(b.year);
              if (keyA > keyB) return -1;
              if (keyA < keyB) return 1;
              return 0;
            });
          }
          break;
        case 'rating':
          this.sortRating = !this.sortRating;
          if(this.sortRating){
            this.filteredList.sort((a, b) => {
              var keyA = Number(a.rating),
                keyB = Number(b.rating);
              if (keyA < keyB) return -1;
              if (keyA > keyB) return 1;
              return 0;
            });
          }else{
            this.filteredList.sort((a, b) => {
              var keyA = Number(a.rating),
                keyB = Number(b.rating);
              if (keyA > keyB) return -1;
              if (keyA < keyB) return 1;
              return 0;
            });
          }
          break;
      }
    },
    filterInput(e, key){
      let val = e.target.value.toLowerCase();
      clearTimeout(this.timeout);
      switch (key) {
        case 'title':
          this.inputTitle = val;
          this.timeout = setTimeout(() => {
            if(val){
              if(this.inputYear && this.inputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key].toLowerCase().includes(val) && item['year'] == this.inputYear && item['rating'] == this.inputRating;
                });
              }else if(this.inputYear){
                this.filteredList = this.list.filter((item)=>{
                  return item[key].toLowerCase().includes(val) && item['year'] == this.inputYear;
                });
              }else if(this.inputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key].toLowerCase().includes(val) && item['rating'] == this.inputRating;
                });
              }else{
                this.filteredList = this.list.filter((item)=>{
                  return item[key].toLowerCase().includes(val);
                });
              }
            }else{
              if(this.inputYear && this.inputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['year'] == this.inputYear && item['rating'] == this.inputRating;
                });
              }else if(this.inputYear){
                this.filteredList = this.list.filter((item)=>{
                  return item['year'] == this.inputYear;
                });
              }else if(this.inputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['rating'] == this.inputRating;
                });
              }else{
                this.filteredList = this.list.filter((item)=>{
                  return true;
                });
              }
            }
          }, this.timeoutDelay);
          break;
        case 'year':
          this.inputYear = val;
          this.timeout = setTimeout(() => {
            if(val){
              if(this.inputTitle && this.inputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] == val && item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] == this.inputRating;
                });
              }else if(this.inputTitle){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] == val && item['title'].toLowerCase().includes(this.inputTitle);
                });
              }else if(this.inputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] == val && item['rating'] == this.inputRating;
                });
              }else{
                this.filteredList = this.list.filter((item)=>{
                  return item[key] == val;
                });
              }
            }else{
              if(this.inputTitle && this.inputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] == this.inputRating;
                });
              }else if(this.inputTitle){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle);
                });
              }else if(this.inputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['rating'] == this.inputRating;
                });
              }else{
                this.filteredList = this.list.filter((item)=>{
                  return true;
                });
              }
            }
          }, this.timeoutDelay);
          break;
        case 'rating':
          this.inputRating = val;
          this.timeout = setTimeout(() => {
            if(val){
              if(this.inputTitle && this.inputYear){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] == val && item['title'].toLowerCase().includes(this.inputTitle) && item['year'] == this.inputYear;
                });
              }else if(this.inputTitle){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] == val && item['title'].toLowerCase().includes(this.inputTitle);
                });
              }else if(this.inputYear){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] == val && item['year'] == this.inputYear;
                });
              }else{
                this.filteredList = this.list.filter((item)=>{
                  return item[key] == val;
                });
              }
            }else{
              if(this.inputTitle && this.inputYear){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['year'] == this.inputYear;
                });
              }else if(this.inputTitle){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle);
                });
              }else if(this.inputYear){
                this.filteredList = this.list.filter((item)=>{
                  return item['year'] == this.inputYear;
                });
              }else{
                this.filteredList = this.list.filter((item)=>{
                  return true;
                });
              }
            }
          }, this.timeoutDelay);
          break;
      }
    }
  }
}
</script>
