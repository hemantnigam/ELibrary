<template>
  <div>
    <div class="overlay" v-if="nameSpinner||isbnSpinner">
      <div class="loader center"></div>
    </div>
    <div id="search" style="text-align: center;">
      <div class="search_header">Search Books</div>
      <div style="text-align: center;">
        <div>
          <input type="text" v-model="search_detail" placeholder="Search">
        </div>

        <div style="display: -webkit-inline-box;">
          <div style="display:flex">
            <div>
              <button class="search_button" @click="search('name')">Search By Name</button>
            </div>
            <div>
              <button class="search_button" @click="search('isbn')">Search By ISBN</button>
            </div>
          </div>
        </div>
      </div>
      <div v-if="hasTableTitleData">
        <b-table
          bordered
          small
          hover
          :items="search_data"
          :fields="fields"
          :current-page="currentPage"
          :per-page="perPage"
        >
          <template slot="actions" slot-scope="row">
            <div>
              <b-button
                @click="showModal"
                @click.stop="info(row.item, row.index, $event.target)"
              >Show Details</b-button>
              <b-modal ref="myModalRef" hide-footer :title="bookdata.title">
                <div class="d-block text-center" style>
                  <!-- {{bookdata}} -->
                  <div v-if="loadBookData">
                    <ul>
                      <li><span class="heading">Title:</span>
                        <span class="description">{{bookdata.title}}</span>
                      </li>
                      <li><span class="heading">Author Name:</span>
                        <span class="description">{{bookdata.author_name}}</span>
                      </li>
                      <li><span class="heading">Subjects:</span>
                        <span class="description" v-for="subject in bookdata.subject" :key="subject">{{subject}}, </span>
                      </li>
                      <li><span class="heading">Languages:</span>
                        <span class="description" v-for="language in bookdata.language" :key="language">{{language}}, </span>
                      </li>
                      <li><span class="heading">Published Year:</span>
                        <span class="description">{{bookdata.first_publish_year}}</span>
                      </li>
                      <li><span class="heading">Publishers:</span>
                        <span class="description" v-for="publisher in bookdata.publisher" :key="publisher">{{publisher}}, </span>
                      </li>
                      <li><span class="heading">Contributors:</span>
                        <span class="description" v-for="contributor in bookdata.contributor" :key="contributor">{{contributor}}, </span>
                      </li>
                    </ul>
                  </div>
                </div>
                <b-btn class="mt-3" block @click="hideModal">Close</b-btn>
              </b-modal>
            </div>
          </template>
        </b-table>
        <b-row>
          <b-col md="6" class="my-1">
            <b-pagination
              :total-rows="totalRows"
              :per-page="perPage"
              v-model="currentPage"
              class="my-0"
              align="center"
            />
          </b-col>
        </b-row>
      </div>
      <div v-if="hasTableISBNData" style="display:flex; width: 90%; margin-left: 20px;">        
        <div><img :src="ISBNData.cover" style="height:400px"></div>
        <div class="ISBNdetails">
        <div><span class="heading">Title: </span> <span class="description">{{ISBNData.title}}</span></div>        
        <div><span class="heading">Authors: </span> <span class="description" v-for="author in ISBNData.authors" :key="author">{{author.name}}, </span></div>
        <div><span class="heading">Subjects: </span> <span class="description" v-for="subject in ISBNData.subjects" :key="subject">{{subject.name}}, </span></div>
        <div><span class="heading">Publishers</span> <span class="description" v-for="publisher in ISBNData.publisher" :key="publisher">{{publisher}}, </span></div>
        <div><span class="heading">publish_date</span> <span class="description">{{ISBNData.publish_date}}</span></div>
        <div><span class="heading">More Info: </span> <span class="description"><a :href="ISBNData.url">{{ISBNData.url}}</a></span></div>
        </div>
      </div>

      <div>
        <b-modal ref="notification" hide-footer>
          <div class="d-block text-center">
            <h3>No Data found for this record</h3>
          </div>
          <b-btn class="mt-3" block @click="hideNotification">Close Me</b-btn>
        </b-modal>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "Home",
  components: {},
  data() {
    return {
      fields: ["title", "author_name", "actions"],
      search_detail: "",
      search_data: [],
      tableData: [],
      searchType: "name",
      hasTableTitleData: false,
      hasTableISBNData: false,
      ISBNData: {},
      currentPage: 1,
      perPage: 5,
      totalRows: "",
      bookdata: {},
      nameSpinner: false,
      isbnSpinner: false,
      loadBookData: false,
      ISBNData:{},
    };
  },
  methods: {
    info(item, index, button) {
      this.loadBookData = false;
      if (item.title != undefined) this.bookdata.title = item.title;
      if (item.author_name != undefined)
        this.bookdata.author_name = item.author_name[0];
      if (item.subject != undefined)
        this.bookdata.subject = item.subject;
      if (item.language != undefined)
        this.bookdata.language = item.language;
      if (item.first_publish_year != undefined)
        this.bookdata.first_publish_year = item.first_publish_year;
      if (item.publisher != undefined)
        this.bookdata.publisher = item.publisher;
      if (item.contributor != undefined)
        this.bookdata.contributor = item.contributor;

      this.loadBookData = true;
    },
    showModal() {
      this.$refs.myModalRef.show();
    },
    hideModal() {
      this.$refs.myModalRef.hide();
    },
    showNotification() {
      this.$refs.notification.show();
    },
    hideNotification() {
      this.$refs.notification.hide();
    },
    search: function(searchType) {
      this.searchType = searchType;
      this.hasTableTitleData = false;
      this.hasTableISBNData = false;
      if (this.searchType == "name") {
        this.searchByName();
      } else if (this.searchType == "isbn") {
        // this.searchByISBN();
        this.searchByISBN_Data();
      }
    },
    searchByName: function() {
      this.nameSpinner = true;
      axios
        .get("https://openlibrary.org/search.json", {
          params: {
            title: this.search_detail
          }
        })
        .then(response => {
          this.search_data = response.data.docs;
          if (this.search_data != "") {
            this.tableData = this.createTableJSON(this.search_data);
            console.log(this.tableData.length);
            this.hasTableTitleData = true;
            this.totalRows = this.tableData.length;
          } else {
            this.showNotification();
          }
          this.nameSpinner = false;
        })
        .catch(error => {
          this.nameSpinner = false;
        });
    },

    searchByISBN_Data:function(){
        this.isbnSpinner = true;
        this.hasTableISBNData = false;
        var that=this;
      axios
        .get("https://openlibrary.org/api/books", {
          params: {
            bibkeys: "ISBN:" + that.search_detail,
            jscmd: "data",
            format: "json"
          }
        })
        .then(response => {
          if (JSON.stringify(response.data) != "{}") {            
            that.search_data = JSON.stringify(response.data);
            
            var startIndex = 0;
            var count = 0;
            for (var i = 0; i < that.search_data.length; i++) {
              if (that.search_data.charAt(i) == "{") count++;

              if (count == 2) {
                startIndex = i;
                break;
              }
            }            
            that.search_data = JSON.parse(that.search_data.substring(startIndex,that.search_data.length-1));            
            if(that.search_data.title!=undefined)
              that.ISBNData.title=that.search_data.title;
            else
              that.ISBNData.title="";
            if(that.search_data.publishers!=undefined)
              that.ISBNData.publisher=that.search_data.publishers[0];
            else
              that.ISBNData.publisher="";
            if(that.search_data.cover!=undefined)
              that.ISBNData.cover=that.search_data.cover.large;
            else
              that.ISBNData.cover=""
            if(that.search_data.url!=undefined)
              that.ISBNData.url=that.search_data.url;
            else
              that.ISBNData.url=""
            if(that.search_data.authors!=undefined)
              that.ISBNData.authors=that.search_data.authors;
            else
              that.ISBNData.authors=""
            if(that.search_data.publish_date!=undefined)
              that.ISBNData.publish_date=that.search_data.publish_date;
            else
              that.ISBNData.publish_date=""
            if(that.search_data.subjects!=undefined)
              that.ISBNData.subjects=that.search_data.subjects;
            else
              that.ISBNData.subjects=""
              
            console.log(that.ISBNData);
            that.hasTableISBNData = true;
          } else {
            this.showNotification();
          }
          this.isbnSpinner = false;
        })
        .catch(error => {
          this.isbnSpinner = false;
        });
    },
    createTableJSON: function(JSONData) {
      var body = [];
      for (var i = 0; i < JSONData.length; i++) {
        var element = {};
        if (
          JSONData[i].title == undefined ||
          JSONData[i].author_name == undefined
        )
          continue;
        element.title = JSONData[i].title;
        element.author = JSONData[i].author_name[0];
        body.push(element);
      }
      return body;
    }
  }
};
</script>

<style>
.search_header {
  font-size: 40px;
  text-align: center;
}
input {
  width: 600px;
  text-indent: 10px;
  color: #4a4848;
}
.page-item.active .page-link {
  background-color: cadetblue;
  border-color: cadetblue;
}
.page-link {
  color: cadetblue;
}
ol,
ul,
dl {
  list-style: none;
  text-align: left;
}
.btn-block:hover {
  border: 1px solid black;
}
.search_button {
  background-color: white;
  border: 1px solid black;
  height: 40px;
  width: 150px;
  margin: 10px;
}
.search_button:hover {
  background-color: cadetblue;
  color: white;
  cursor: pointer;
}
.btn {
  border-radius: 0px !important;
  height: 35px !important;
  border: 1px solid black !important;
  background-color: white;
  color: black !important;
}
.btn:hover {
  background-color: cadetblue !important;
  color: white !important;
  cursor: pointer !important;
}
thead {
  background-color: cadetblue;
  color: white;
}
.table {
  width: calc(100% / 1.25);
  border: 1px solid black;
  margin: 0 auto;
}
.btn-secondary {
  color:black !important;
  background-color: white !important;
    border-color: black !important;
    border-radius: 0px !important;
}
.mb-1,
.my-1 {
  margin: 0 auto;
}
.overlay {
  position: fixed;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  /* z-index: 2; */
  cursor: pointer;
}
.center {
  display: block;
  top: 40%;
  position: fixed;
  left: 46%;
}
.loader {
  border: 16px solid #f3f3f3;
  border-radius: 50%;
  border-top: 16px solid cadetblue;
  border-bottom: 16px solid cadetblue;
  width: 120px;
  height: 120px;
  -webkit-animation: spin 2s linear infinite;
  animation: spin 2s linear infinite;
}

@-webkit-keyframes spin {
  0% {
    -webkit-transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
  }
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
.description {
  font-size: 14px;
}
.heading{
  font-weight: bold
}
.ISBNdetails{
  text-align: left;
    margin-top: 50px;
    margin-left: 30px;
}
</style>
