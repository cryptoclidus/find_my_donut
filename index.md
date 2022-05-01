Welcome to the donut finder! Head over to <a href="https://andrewprice.art/17731-first-steps/donuts" target="_blank">Andrew Price's Donut Database</a> to find your donut number. Then enter your number below and the Finder will try to find your donut in the mosaic using image recognition! 

<html>
    <head>
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
        <script src="https://docs.opencv.org/3.3.1/opencv.js" type="text/javascript"></script>
        <script src="https://cryptoclidus.github.io/find_my_donut/donut_slice_0.js"></script>
        <script src="https://cryptoclidus.github.io/find_my_donut/donut_slice_1.js"></script>
        <script src="https://cryptoclidus.github.io/find_my_donut/donut_slice_2.js"></script>
        <script src="https://cryptoclidus.github.io/find_my_donut/donut_slice_3.js"></script>
        <script src="https://cryptoclidus.github.io/find_my_donut/donut_slice_4.js"></script>
        <script src="https://cryptoclidus.github.io/find_my_donut/donut_slice_5.js"></script>
        <script src="https://cryptoclidus.github.io/find_my_donut/donut_slice_6.js"></script>
        <script src="https://cryptoclidus.github.io/find_my_donut/donut_slice_7.js"></script>
        <script src="https://cryptoclidus.github.io/find_my_donut/donut_slice_8.js"></script>
        <script src="https://cryptoclidus.github.io/find_my_donut/donut_slice_9.js"></script>
    </head>
    <body>

        <label for="fdonut-id">Donut number:</label>
        <input type="number" id="fdonut-id" name="fdonut-id" min=1 max=17731 required><br><br>
        <button type="button" id="submitBtn">Submit</button>

        <h3 id="name"></h3>
        <h4 id="country"></h4>
        <h5 id="donut-no"></h5>
        <img id="template"/>
        
        <div id="donut-locator">
            <canvas id="result"></canvas>
        </div>
        <p id="caption"></p>
        <img id="tile"/>
        <div id="donut_small_div"></div>
        

        
    </body>
    <script>
        const canvas = document.getElementById('result');
        const context = canvas.getContext('2d');
        const donut_slices = [
            donut_slice_0,
            donut_slice_1,
            donut_slice_2,
            donut_slice_3,
            donut_slice_4,
            donut_slice_5,
            donut_slice_6,
            donut_slice_7,
            donut_slice_8,
            donut_slice_9
        ]
        // resize image 
        function resize(src, width, height){
            let dst = new cv.Mat();
            let dsize = new cv.Size(width, height);
            // You can try more different parameters
            cv.resize(src, dst, dsize, 0, 0, cv.INTER_AREA);
            return dst;
        }
        // Get random donut int
        function getRandomInt(max) {
            return Math.floor(Math.random() * max)+1;
        }
        // callback function to wait for image to load
        function onImgLoad(selector){
            let imageElement = document.getElementById(selector);
            return new Promise(resolve=>{imageElement.onload = resolve})
        }


        // function to get donut url from id#
        function getDonutUrl(number) {
            $.ajax({
                    url: "https://ci6l4fta7s-dsn.algolia.net/1/indexes/crypdonut_submissions/"+String(number)+"?x-algolia-agent=Algolia%20for%20JavaScript%20(4.9.1)%3B%20Browser",
                    headers : {
                        "x-algolia-api-key": "348c4f2567a8ea8e406acdea18138b14",
                        "x-algolia-application-id": "CI6L4FTA7S"}       
                 }).done(function(data) {
                     $("#template").attr("crossOrigin","");
                     $('#country').text(data.Country);
                     $("#template").attr("src", "https://poliigon.com/cdn-cgi/image/width=256,f=auto/"+data.BlenderFile+'?'+new Date().getTime());
                     $("#name").text(data.FullName + "'s Donut");
                     $("#donut-no").text("Donut #"+data.objectID);
                });
        }
        function findDonut(number) {
            //clear the html fields
            $("#template").attr("src", "");
            $('#country').text("");
            $("#name").text("");
            $("#donut-no").text("");
            $("#caption").text("");
            $("#tile").attr("src","");
            
            
            context.clearRect(0, 0, canvas.width, canvas.height);

            //get donut url and load image
            url = getDonutUrl(number);
                    
            
            //initialize max point values
            let currMax;
            let currPoint;
            let currSlice;
            
            //when image loaded, read donut img into a mat
            onImgLoad("template").then(()=>{
                let templateSrcElement = document.getElementById("template");
                let templ = cv.imread(templateSrcElement);
                let canvasElement = document.getElementById("result");
                
                //resize template
                templ = resize(templ,32,32);
                
                // convert to black and white
                let dst_mat = new cv.Mat();
                cv.cvtColor(templ, dst_mat, cv.COLOR_BGRA2GRAY, dstCn=0);
                templ = dst_mat;
                delete dst_mat;
                
                // perform match
                currMax = 0;
                
                // look for match in donut slices
                for (let i = 0; i < 10; i++) {
                    let src = cv.matFromArray(544,5440,cv.CV_8U,donut_slices[i]);
                    let dst = new cv.Mat();
                    let mask = new cv.Mat();


                    cv.matchTemplate(src, templ, dst, cv.TM_CCORR_NORMED, mask);
                    delete src;
                    let result = cv.minMaxLoc(dst, mask);
                    delete mask;
                    delete dst;
                    let maxPoint = result.maxLoc;
                    let maxVal = result.maxVal;
                    
                    
                    if (maxVal > currMax) {
                        currMax = maxVal;
                        currPoint = maxPoint;
                        currSlice = i;
                    }
                    
                }
                    
                // draw rectangle on the small donut image
                //load donut_small image
                smallDonut = $("<img id='donut_small' src='donut_small.png'/>");
                $("#donut_small_div").append(smallDonut);
                
                onImgLoad("donut_small").then(()=>{
                    donutSmall = cv.imread(document.getElementById("donut_small"));
                    //draw a point on the donut
                    $("#donut_small").prop('width', "256px"); 
                    
                    let color = new cv.Scalar(255, 0, 0, 255);
                    let topLeft = new cv.Point(Math.floor(currPoint.x/16), Math.floor(currSlice*34 + currPoint.y/16));
                    let bottomRight = new cv.Point(topLeft.x + 3, topLeft.y + 3);

                    cv.rectangle(donutSmall, topLeft, bottomRight, color, 2, cv.LINE_8, 0);
                    //hide old donut, show new
                    resized = resize(donutSmall,256,256);
                    delete donutSmall;
                    cv.imshow(canvasElement, resized);
                    delete resized;
                    $("#donut_small_div").html("");
               });
                
                // get tile with donut
                let centerPoint = new cv.Point((currPoint.x + 16)*4 - 7, (currSlice*544 + currPoint.y + 16)*4 + 36);
                let level = 15;
                let tile_x = Math.floor(centerPoint.x / 254);
                let tile_y = Math.floor(centerPoint.y / 254);
                https://s3.us-east-2.amazonaws.com/cdn.crypdonuts.guru/donut_files/14/33_28.jpeg
                $("#tile").attr("src","https://s3.us-east-2.amazonaws.com/cdn.crypdonuts.guru/donut_files/"+String(level)+"/"+String(tile_x)+"_"+String(tile_y)+".jpeg");
                

                $("#caption").text("Is this your donut in the image below?");
            }).catch(function(rejected){
                console.log(rejected);;
            }).catch(function(rejected){
                console.log(rejected);
            });
        }
        // Main
        $(document).ready(function(){

            //get random donut int or check input
            let donut_id;
            if (!$("#fdonut-id").val()) {
                donut_id = getRandomInt(17731);
            } else {
                donut_id = $("#fdonut-id").val()
            }
            
            findDonut(donut_id);
            
            //activate button
            $("#submitBtn").click(function(){
                //validate form field
                donut_id = $("#fdonut-id").val()
                
                if (donut_id <= 17731 && donut_id >=1){
                    findDonut(donut_id);
                } else {
                    alert("Must be a number from 1 to 17731");
                }
            });            
        });
    </script>
</html>
*This page is fan made by donut contributor #4314 and is not affiliated with our favorite teacher of blender donut modelling Andrew Price. All images are sourced from [https://andrewprice.art/](https://andrewprice.art/).*
