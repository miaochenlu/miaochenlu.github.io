# Internal and External CSS
## A. Internal
```html
<head> 
    <title>Using Internal CSS</title>
    <style type="text/css"> 
    body {
        font-family: arial; background-color: rgb(185,179,175);
    } 
    h1 { 
        color: rgb(255,255,255);
    } 
    </style> 
</head> 
<body> 
    <h1>Potatoes</h1> 
    <p>There are dozens of different potato varieties. They are usually described as early, second early and maincrop.</p> 
</body>
```
## B. External
```html
<head> 
    <title>Using External CSS</title> 
    <link href="css/styles.css" type="text/css" rel="stylesheet" /> 
</head> 
<body> 
    <h1>Potatoes</h1> 
    <p>There are dozens of different potato varieties. They are usually described as early, second early and maincrop.</p> 
</body>
```
<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200414215321829.png" alt="image-20200414215321829" style="zoom:50%;" />

# CSS Selectors

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200414215639009.png" alt="image-20200414215639009" style="zoom:50%;" />

**Last rule**: If the two selectors are identical, the latter of the two will take precedence.
**Specificity**: If one selector is more specific than the others, the more specific rule will take precedence over more general ones. e.g. h1 is more specific than * p b is more specific than p p#intro is more specific than p.

# Color
可以用**RGB values**, **Hex codes**, **Color Names**来表示颜色
```html
<head>
    <style type="text/css">
    body { 
        background-color: rgb(200,200,200);
    } 
    h1 {
        background-color: DarkCyan;
    }
    h2 {
        background-color: #ee3e80;
    } 
    p {
        background-color: white;
    }
    </style>
</head>
<body>
    <h1>Marine Biology</h1>
    <h2>The composition sweater</h2>
    <p>some contents</p>
</body>
```


<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200414220406621.png" alt="image-20200414220406621" style="zoom:50%;" />


# Text

## 1. specifying typefaces: font-family

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416144713469.png" alt="image-20200416144713469" style="zoom:50%;" /> 

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416144734928.png" alt="image-20200416144734928" style="zoom:50%;" />

```html
<head> 
    <title>Font Family</title> 
    <style type="text/css"> 
    body { 
        font-family: Georgia, Times, serif;
    } 
    h1, h2 { 
        font-family: Arial, Verdana, sans-serif;
    } 
    .credits { 
        font-family: "Courier New", Courier, monospace;
    } 
    </style> 
</head> 
<body> 
    <h1>Briards</h1> 
    <p class="credits">by Ivy Duckett</p> 
    <p class="intro">The <a class="breed" href="http://en.wikipedia.org/wiki/ Briard">briard</a>, or berger de brie, is a large breed of dog traditionally used as a herder and guardian of sheep...</p> 
</body>
```


<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416144521652.png" alt="image-20200416144521652" style="zoom:50%;" />

## 2. size of type: font-size

There are several ways to specify the size of a font. 

* ***pixels***

* ***percentages***:  The default size of text in browsers is 16px. So a size of 75% would be the equivalent of 12px, and 200% would be 32px.

If you create a rule to make all text inside the `<body>` element to be 75% of the default size (to make it 12px), and then specify another rule that indicates the content of an element inside the `<body>` element should be 75% size, it will be 9px (75% of the 12px font size).

* ems: An em is equivalent to the width of a letter m.

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416145246754.png" alt="image-20200416145246754" style="zoom:50%;" />

## 3. More font choices: @font-face
@font-face allows you to use a font, even if it is **not installed** on the computer of the person browsing, by allowing you to specify a path to a copy of the font, which will be downloaded if it is not on the user's machine.
```css
@font-face { 
    font-family: 'ChunkFiveRegular'; 
    src: url('fonts/chunkfive.eot'); 
    src: url('fonts/chunkfive.eot?#iefix') 
        format('embedded-opentype'), 
        url('fonts/chunkfive.woff') 
        format('woff'), 
        url('fonts/chunkfive.ttf') 
        format('truetype'), 
        url('fonts/chunkfive.svg#ChunkFiveRegular') 
        format('svg');
}
```