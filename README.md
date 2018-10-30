技術
===

### Backend Programming Language: C#、Linq、Node.js
主要使用的語言是C#，另因看好node.js未來性故自主學習。

- node.js範例程式碼：

```node.js=
//example:使用express架構，連接omdbapi webapi取得資料，利用ejs樣板引擎建立檢視頁面顯示Json 檔案及資料
//(僅節錄主程式程式碼)
var express = require('express');
var request = require('request');
var bodyParser = require('body-parser');

var app = express();
var port = process.env.PORT || 3000;

app.use(bodyParser.urlencoded({ extend:false}));
app.use(bodyParser.json());

//get search condition & post back api result
app.post('/condition', function(req, res) {        
    // get search parameter 
    var content = req.body.search;        
    request('http://www.omdbapi.com/?s='+content+'&y=&plot=short&r=json',
        function (error, response, body) {    
            if(error){
                return console.log('Error:', error);
            }    
            if(response.statusCode !== 200){
                return console.log('Invalid Status Code Returned:',
                response.statusCode);
            }
            //parsed to json type
            var result =  (JSON.parse(body)).Search;
            //redirect to new page and post data
            res.render('testHB.ejs',{ body:  result});
        });        
});

//launch
app.listen(port);
```

### framework：WebForm(MVC 3,5)、.net Core(2.1)、web api(2)
具備MVC(C#)兩年多的經歷，另因看好.net Core的未來發展故自主學習.net core(2.1)。而web api則是因現行主流架構故學習之。

- web api搭配linq範例程式碼：
```c#=
//example:撰寫api=>取得學生基本資料
//controller
public class StudentController : ApiController
    {
        Student[] students = new Student[] 
        { 
            new Student { Id = 1, Name = "Toma", Weight = 65, Height = 155, Gender= "F" }, 
            new Student { Id = 2, Name = "Yo-yo", Weight = 80, Height = 165, Gender = "F"},
            new Student { Id = 3, Name = "Hamm", Weight = 77, Height = 173, Gender = "M"},
            new Student { Id = 4, Name = "Kdd", Weight = 90, Height = 182, Gender = "M"}
        };
        
        [Route("api/students")]
        public IEnumerable<Student> GetAllStudents()
        {
            return students;
        }

        [Route("api/students/id/{id}")]
        public IHttpActionResult GetStudentByID(int id)
        {
            var student = students.FirstOrDefault((p) => p.Id == id);
            if (students == null)
            {
                return NotFound();
            }
            return Ok(students);
        }
        
        [Route("api/students/Gender")]
        public IHttpActionResult GetGenderAvgWeight()
        {
            //取得不同性別的平均體重
            var student = students.GroupBy(t => new { Gender = t.Gender} ).Select(c => new
            {
                AvgWeight = c.Average( p=>p.Weight),
                AvgGender = c.Key.Gender
            });
        
        }
    }

//model
public class Student
{
    public int Id {get;set;}
    public string Name {get;set;}
    public int Weight {get;set;}
    public int Height {get;set;}
    public string Gender { get; set; }
}
    
```

### Front Programming Language: Javascript ES5、Jquery、Vue.js
主要使用的語言是Javascript ES5及Jquery，Vue.js主要是用在自己的side project。

- vue.js範例程式碼：
```html=
<!--example:連接api服務取得圖片資料(json檔案)，搭配vue.js呈現畫面-->
<!--(未節錄css)-->
<html>
    <head>
        <meta charset="UTF-8">
        <title>This is test page</title>  
    </head>
    <body>
        <div id="adata" v-cloak>                                     
                    <ul class = "portfolio-area"  >                        
                        <li class="portfolio-item2" v-bind:data-id="'id-'+album.gphoto$id.$t" v-for="album in albums | orderBy 'media$group.media$title.$t' -1 | filterBy searchKey | paginate ">
                            <div>
                                <span class="image-block">
                                    <a class="image-zoom" v-bind:href="pageurl+album.gphoto$id.$t">
                                        <div class="nailthumb-container">
                                            <img v-bind:src="coverurl(album.media$group.media$content[0].url)" />
                                        </div>
                                    </a>
                                </span>
                                <div class="home-portfolio-text">
                                    <h2 class="post-title-portfolio">
                                        <a rel="bookmark" v-bind:title="album.title.$t">{{album.title.$t}}</a>
                                    </h2>
                                    <p class="post-subtitle-portfolio"> 共 {{album.gphoto$numphotos.$t}} 張圖片 </p>
                                </div>
                            </div>
                        </li>                                               
                        <div class="column-clear"></div>    
                    </ul>

                    <br/>
                    <!--pagination-->					
                    <ul class="pagination">
                        <li><a href="#" @click="setPage(0)" id="tfirst">«</a></li>
                         <li v-for="pageNumber in totalPages" > 
                             <a href="#" @click="setPage(pageNumber)"  :class="{current: currentPage === pageNumber, last: (pageNumber == totalPages - 1), first:(pageNumber == 0 )}">{{pageNumber+1}}</a> 
                         </li> 
                         <li><a href="#" @click="setPage(totalPages)" id="tlast">»</a></li> 
                    </ul>
                </div>
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
        <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/vue/1.0.6/vue.min.js"></script>
    </body>
    <script>
    var url = "xxx...";    //webapi 
    $.get(url, function () {})    //get data
    .done(function (data) {        
        getView(data);  //vue generate
    })
        var getView = function (data) {
    new Vue({
        el: '#adata',
        data: {
            albums: data,
            pageurl: 'phdetail.html?albumid=',
            searchKey: '',      /*pagingation*/
            itemsPerPage: 20,
            resultCount: 0            
        },
        computed: {
            totalPages: function () {                
                return Math.ceil(this.resultCount / this.itemsPerPage)
            }
        },
        methods: {
            //重新產生圖片url
            coverurl: function (e) {                
                var boxss = [];
                boxss = e.split("/");
                var resulturl = "";
                for (var i = 0; i < boxss.length; i++) {
                    if (i == boxss.length - 1) {
                        resulturl += "s240/";   
                    }
                    resulturl += boxss[i];
                    if (i == boxss.length - 1) { }
                    else {
                        resulturl += "/";
                    }
                }                
                return resulturl;
            }
        },
        setPage: function (pageNumber) {            
            this.currentPage = pageNumber;
        },        
        filters: {
            //設定分頁
            paginate: function (list) {
                this.resultCount = list.length; 
                if (this.currentPage >= this.totalPages) {
                    this.currentPage = this.totalPages - 1
                }
                var index = this.currentPage * this.itemsPerPage
                return list.slice(index, index + this.itemsPerPage)
            }
        }
        
    });
}
    </script>
</html>    
```

### Database：SQL Server、Oracle、Mongodb
資料庫的部分主要使用SQL Server以及Oracle，包含View、Stored Procedure的使用。
mongodb 只用在我的 side project.






