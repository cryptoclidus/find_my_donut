<html>
    <head>
        <link rel="stylesheet" href="stylesheet.css">
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
 <script src="https://docs.opencv.org/3.3.1/opencv.js" type="text/javascript"></script>
        <script>
            $(document).ready(function(){
                // Random donut int
                function getRandomInt(max) {
                    return Math.floor(Math.random() * max)+1;
                }
                // callback function to wait fot image to load
                var onImgLoad = function(selector, callback){
                    $(selector).each(function(){
                        if (this.complete || /*for IE 10-*/ $(this).height() > 0) {
                            callback.apply(this);
                        }
                        else {
                            $(this).on('load', function(){
                                callback.apply(this);
                            });
                        }
                    });
                };
                
                // resize image 
                function resize(src, width, height){
                    let dst = new cv.Mat();
                    let dsize = new cv.Size(width, height);
                    // You can try more different parameters
                    cv.resize(src, dst, dsize, 0, 0, cv.INTER_AREA);
                    return dst;
                }
                
                function match(row,column, level, x_donuts){
                    
                    var template_url = "https://poliigon.com/cdn-cgi/image/width=32,f=auto/";
                            var donut_url = $("#donut-img").attr("src");
                            $("#donut-resized1").attr("src", template_url + donut_url);
                            
            
                            $("#test-tile").attr("src","https://s3.us-east-2.amazonaws.com/cdn.crypdonuts.guru/donut_files/"+level+"/"+row+"_"+column+".jpeg");
                            $("#searching").text("Searching in "+row+"_"+column);
                            
                            
                    
                    onImgLoad("#test-tile",function(){
                        
                    

                    let imgSrcElement = document.getElementById("test-tile");
                    let templateSrcElement = document.getElementById("donut-img");
                    let canvasElement = document.getElementById("donut-locator");
                    let donutElement = document.getElementById("donut-resized");
                    
                    let src = cv.imread(imgSrcElement);
                    let templ = cv.imread(templateSrcElement);
                    // Creating the empty destination matrix
                    let dst_mat = new cv.Mat();
                    let dst_mat1 = new cv.Mat();
                    
                    let dst = new cv.Mat();
                let mask = new cv.Mat();
                
                    cv.cvtColor(src, dst_mat, cv.COLOR_BGRA2BGR, dstCn=0);
                    src = dst_mat;
                    cv.cvtColor(templ, dst_mat1, cv.COLOR_BGRA2BGR, dstCn=0);
                    templ = dst_mat1;

                    // Converting the image from BGRA to BGR and saving it in the dst_mat matrix
                    
                templ = resize(templ,256/x_donuts,256/x_donuts);
                cv.imshow(donutElement,templ);
                
                
                cv.matchTemplate(src, templ, dst, cv.TM_CCORR_NORMED, mask);
                /*
                let result = cv.minMaxLoc(dst, mask);
let maxPoint = result.maxLoc;
let color = new cv.Scalar(255, 0, 0);
let point = new cv.Point(maxPoint.x + templ.cols, maxPoint.y + templ.rows);
                cv.rectangle(src, maxPoint, point, color, 1, cv.LINE_8, 0);
                */
                let color = new cv.Scalar(255, 0, 0);
                
                var newDst = [];
                var start = 0;
                var end = dst.cols;
                        var matches = 0;
                
                for (var i = 0; i < dst.rows; i++) {
                    
                    newDst[i] = [];
                            
                    for (var k = 0; k < dst.cols; k++) {
                        
                        newDst[i][k] = dst.data32F[start];
                        
                        if (newDst[i][k] > 0.98) {
                                            matches += 1;
                            $("#searching").text("match found!");
                            let maxPoint = {
                                "x": k,
                                "y": i
                            }
                            let point = new cv.Point(k + templ.cols, i + templ.rows);
                            cv.rectangle(src, maxPoint, point, color, 1, cv.LINE_8, 0);
                        }
                        start++;
                    }
                    start = end;
                    end = end + dst.cols;
                }
                        if (matches > 0) {
                            cv.imshow(canvasElement, src);
                        }
                        
                        // cv.imshow(canvasElement, src);
                    //cv.matchTemplate(donutMosaic,donutImg);
                    
                    //$("#test-tile").attr("src","");
                    //$("#test-tile").attr("src","https://s3.us-east-2.amazonaws.com/cdn.crypdonuts.guru/donut_files/13/"+String(row)+"_"+String(column)+".jpeg");
                    //start with level 11, 5x5, 24 donuts per sqare
                    //level 12, 10x10, 16 donuts per square
                    //level 13, 21x21, 8 donuts per square
                    //level 14, 42x42, 4 donuts per square
                });
                }
                 
                
                // check complete
                //do image match
                function findDonut(){
                    //https://s3.us-east-2.amazonaws.com/cdn.crypdonuts.guru/donut_files/13/16_14.jpeg
                    //256/8
                    //https://s3.us-east-2.amazonaws.com/cdn.crypdonuts.guru/donut_files/14/33_28.jpeg
                    //256/4
                    let level = 13;
                    let x_donuts = 8;
                 let rows = 21;
                let cols = 21;
                    
                    var row = 0;
                    var column = 0;
                    
                    //match(16,14,13,8)
                    


function f() {
  match(row,column, level, x_donuts)
  if (column < cols){
      column++;
      setTimeout(f,500);
  } else if (row < rows) {
      column = 0;
      row++;
    setTimeout(f, 500);
  }
}

f();

 
                    
                    
                            
                    
                }
                // Get donut info from number
                function search(number){
                    
                    $.ajax({
                    url: "https://ci6l4fta7s-dsn.algolia.net/1/indexes/crypdonut_submissions/"+String(number)+"?x-algolia-agent=Algolia%20for%20JavaScript%20(4.9.1)%3B%20Browser",
                    headers : {
                        "x-algolia-api-key": "348c4f2567a8ea8e406acdea18138b14",
                        "x-algolia-application-id": "CI6L4FTA7S"}       
                 }).done(function(data) {
                     
                     $('#country').text(data.Country);
                     $("#donut-img").attr("src", "https://poliigon.com/cdn-cgi/image/width=256,f=auto/"+data.BlenderFile);
                     $("#name").text(data.FullName + "'s Donut");
                     $("#donut-no").text("Donut #"+data.objectID);
                     $("#searching").text("searching for Donut#"+data.objectID+"...");
                     
                     onImgLoad("#donut-img",function(){
                         findDonut();
                     });
                 });
             
                 }
                 
                 // Load donut on submit
                 $("#submitBtn").click(function(){
                     var number = parseInt($("#fdonut-id").val());
                    //validate form field
                    if (number <= 17731 && number >=1){
                        search(number);
                    } else {
                        alert("Must be a number from 1 to 17731");
                    }
                });
                
                //set a random donut on document loaded
                
                var randomDonut = getRandomInt(17731);
                randomDonut = 4314; //debugging
                $("#fdonut-id").val(randomDonut);
                search(randomDonut);
                
            });
         
        </script>
    </head>
    <body>
        <form>
            <label for="fdonut-id">Donut number:</label>
            <input type="number" id="fdonut-id" name="fdonut-id" value=1 min=0 max=17731 required><br><br>
            <button type="button" id="submitBtn">Submit</button>
        </form>
        <div id="donut-no"></div>        
        <div id="name"></div>
        <div id="country"></div>
        <img id="donut-img" name="donut-img"/>
        <div id="searching"></div>
        <img id="test-tile" />
        <img id+"donut-resized1"/>
        <canvas id="donut-resized"></canvas>
        <canvas id="donut-locator"></canvas>

    </body>

</html>
You can use the editor on GitHub to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run Jekyll to rebuild the pages in your site, from the content in your Markdown files.

Markdown
Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
For more details see Basic writing and formatting syntax.

Jekyll Themes
Your Pages site will use the layout and styles from the Jekyll theme you have selected in your repository settings. The name of this theme is saved in the Jekyll _config.yml configuration file.

Support or Contact
Having trouble with Pages? Check out our documentation or contact support and we’ll help you sort it out.
