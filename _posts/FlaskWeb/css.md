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

## Opacity
<head>
<style type="text/css">
p.one { background-color: rgb(0,0,0); opacity: 0.5;} p.two { background-color: rgb(0,0,0); background-color: rgba(0,0,0,0.5);}
</style>
</head>
<body>
<p class="one">p.one</p>
<p class="two">p.two</p>
</body>