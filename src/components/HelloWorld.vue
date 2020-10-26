<template>
  <div>
    <h1>{{ msg }}</h1>

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
    <div>Total {{ filteredList.length }}</div>
    <div class="table-wrapper">
        <div class="header-table col-flex-center">
            <div class="header-key header-index">Index</div>
            <div class="header-key header-title"  @click="sortBy('title')">
              <span class="header-year-title" @click="sortBy('title')">Title</span>
              <input type="text" placeholder="search" @keyup="filterInput($event, 'title', 'title')">
            </div>
            <div class="header-key header-year">
              <span class="header-year-text padding-left-05 padding-right-05" @click="sortBy('year')">Year<i class="margin-left-1 arrow" :class="{'down':!sortYear, 'up':sortYear}"></i></span>
              <div>
                Min:<input type="text" placeholder="search" @keyup="filterInput($event, 'min-year', 'year', true)">
              </div>
              <div>
                Max:<input type="text" placeholder="search" @keyup="filterInput($event, 'max-year', 'year', true)">
              </div>
            </div>
            <div class="header-key header-status"  @click="sortBy('status')">Status</div>
            <div class="header-key header-rating">
              <span class="header-rating-text padding-left-05 padding-right-05" @click="sortBy('rating')">Rating<i class="margin-left-1 arrow" :class="{'down':!sortRating, 'up':sortRating}"></i></span>
              <div>
                Min:<input type="text" placeholder="search" @keyup="filterInput($event, 'min-rating', 'rating',  true)">
              </div>
              <div>
                Max:<input type="text" placeholder="search" @keyup="filterInput($event, 'max-rating', 'rating', true)">
              </div>
            </div>
            <div class="header-key header-link"  @click="sortBy('link')">Link</div>
            <div class="header-key header-description"  @click="sortBy('description')">Description</div>
        </div>
        <div v-for="(item, i) in filteredList" :key="item" class="content-wrapper col-flex-center">
            <div class="content-key content-index">{{ i+1 }}</div>
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
      list: [],
      filteredList: [],
      inputTitle: '',
      minInputYear: '',
      maxInputYear: '',
      minInputRating: '',
      maxInputRating: '',
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
    filterInput(e, metakey, key, isNumber){
      let val;
      if(isNumber){
        val = Number(e.target.value);
      }else{
        val = e.target.value.toLowerCase();
      }
      clearTimeout(this.timeout);
      switch (metakey) {
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
        case 'min-year':
          this.minInputYear = val;
          this.timeout = setTimeout(() => {
            if(val){
              if(this.inputTitle && this.maxInputYear && this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['year'] <= this.maxInputYear && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle && this.maxInputYear && this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['year'] <= this.maxInputYear && item['rating'] >= this.minInputRating;
                });
              }else if(this.inputTitle && this.maxInputYear && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['year'] <= this.maxInputYear && item['rating'] <= this.maxInputRating;
                });
              }else if(this.maxInputYear && this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['year'] <= this.maxInputYear && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle && this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle && this.maxInputYear){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['year'] <= this.maxInputYear;
                });
              }else if(this.inputTitle && this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] >= this.minInputRating;
                });
              }else if(this.inputTitle && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] <= this.maxInputRating;
                });
              }else if(this.maxInputYear && this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['year'] <= this.maxInputYear && item['rating'] >= this.minInputRating;
                });
              }else if(this.maxInputYear && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['year'] <= this.maxInputYear && item['rating'] <= this.maxInputRating;
                });
              }else if(this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle);
                });
              }else if(this.maxInputYear){
                console.log('got here', val, 'maxInputYear',this.maxInputYear);
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['year'] <= this.maxInputYear;
                });
                console.log('got here after ', this.filteredList.length);
              }else if(this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['rating'] >= this.minInputRating;
                });
              }else if(this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['rating'] <= this.maxInputRating;
                });
              }else{
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val;
                });
              }
            }else{
              if(this.inputTitle && this.maxInputYear && this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['year'] <= this.maxInputYear && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle && this.maxInputYear && this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['year'] <= this.maxInputYear && item['rating'] >= this.minInputRating;
                });
              }else if(this.inputTitle && this.maxInputYear && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['year'] <= this.maxInputYear && item['rating'] <= this.maxInputRating;
                });
              }else if(this.maxInputYear && this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['year'] <= this.maxInputYear && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle && this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle && this.maxInputYear){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['year'] <= this.maxInputYear;
                });
              }else if(this.inputTitle && this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] >= this.minInputRating;
                });
              }else if(this.inputTitle && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] <= this.maxInputRating;
                });
              }else if(this.maxInputYear && this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['year'] <= this.maxInputYear && item['rating'] >= this.minInputRating;
                });
              }else if(this.maxInputYear && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['year'] <= this.maxInputYear && item['rating'] <= this.maxInputRating;
                });
              }else if(this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle);
                });
              }else if(this.maxInputYear){
                this.filteredList = this.list.filter((item)=>{
                  return item['year'] <= this.maxInputYear;
                });
              }else if(this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['rating'] >= this.minInputRating;
                });
              }else if(this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['rating'] <= this.maxInputRating;
                });
              }else{
                this.filteredList = this.list.filter((item)=>{
                  return true;
                });
              }
            }
          }, this.timeoutDelay);
          break;
        case 'max-year':
          this.maxInputYear = val;
          this.timeout = setTimeout(() => {
            if(val){
              if(this.inputTitle && this.minInputYear && this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['year'] >= this.minInputYear && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle && this.minInputYear && this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['year'] >= this.minInputYear && item['rating'] >= this.minInputRating;
                });
              }else if(this.inputTitle && this.minInputYear && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['year'] >= this.minInputYear && item['rating'] <= this.maxInputRating;
                });
              }else if(this.minInputYear && this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['year'] >= this.minInputYear && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle && this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle && this.minInputYear){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['year'] >= this.minInputYear;
                });
              }else if(this.inputTitle && this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] >= this.minInputRating;
                });
              }else if(this.inputTitle && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] <= this.maxInputRating;
                });
              }else if(this.minInputYear && this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['year'] >= this.minInputYear && item['rating'] >= this.minInputRating;
                });
              }else if(this.minInputYear && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['year'] >= this.minInputYear && item['rating'] <= this.maxInputRating;
                });
              }else if(this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['title'].toLowerCase().includes(this.inputTitle);
                });
              }else if(this.minInputYear){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['year'] >= this.minInputYear;
                });
              }else if(this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['rating'] >= this.minInputRating;
                });
              }else if(this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val && item['rating'] <= this.maxInputRating;
                });
              }else{
                this.filteredList = this.list.filter((item)=>{
                  return item[key] >= val;
                });
              }
            }else{
              if(this.inputTitle && this.minInputYear && this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['year'] >= this.minInputYear && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle && this.minInputYear && this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['year'] >= this.minInputYear && item['rating'] >= this.minInputRating;
                });
              }else if(this.inputTitle && this.minInputYear && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['year'] >= this.minInputYear && item['rating'] <= this.maxInputRating;
                });
              }else if(this.minInputYear && this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['year'] >= this.minInputYear && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle && this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle && this.minInputYear){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['year'] >= this.minInputYear;
                });
              }else if(this.inputTitle && this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] >= this.minInputRating;
                });
              }else if(this.inputTitle && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle) && item['rating'] <= this.maxInputRating;
                });
              }else if(this.minInputYear && this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['year'] >= this.minInputYear && item['rating'] >= this.minInputRating;
                });
              }else if(this.minInputYear && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['year'] >= this.minInputYear && item['rating'] <= this.maxInputRating;
                });
              }else if(this.minInputRating && this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['rating'] >= this.minInputRating && item['rating'] <= this.maxInputRating;
                });
              }else if(this.inputTitle){
                this.filteredList = this.list.filter((item)=>{
                  return item['title'].toLowerCase().includes(this.inputTitle);
                });
              }else if(this.minInputYear){
                this.filteredList = this.list.filter((item)=>{
                  return item['year'] >= this.minInputYear;
                });
              }else if(this.minInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['rating'] >= this.minInputRating;
                });
              }else if(this.maxInputRating){
                this.filteredList = this.list.filter((item)=>{
                  return item['rating'] <= this.maxInputRating;
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
      console.log('FINISH FILTER', val, this.filteredList);
    }
  }
}
</script>
