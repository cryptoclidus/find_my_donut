<form>
  <label for="fname">Donut Number:</label>
  <input type="text" id="donutnumber" name="donutnumber"><br><br>
</form>
<script src="https://code.jquery.com/jquery-3.6.0.min.js" type="text/javascript"></script>
<script>
  $().ready(function() {
   $("#donut-img").attr('src','https://poliigon.com/cdn-cgi/image/width=256,f=auto/https://cdn.crypdonuts.guru/submissions/19lrbMqWZ6GiGRgfQBjQtuSeecer0Beto.png');
    $( ".donut-img" ).load( "https://andrewprice.art/17731-first-steps/donut/7473", function(x) {
      $("#donut-img1").attr('src', x.attr('src'));
    });
  });
</script>

<img id="donut-img">
<img id="donut-img1">
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
