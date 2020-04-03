---
layout: article
titles:
  en      : &EN       About
  en-GB   : *EN
  en-US   : *EN
  en-CA   : *EN
  en-AU   : *EN
  zh-Hans : &ZH_HANS  关于
  zh      : *ZH_HANS
  zh-CN   : *ZH_HANS
  zh-SG   : *ZH_HANS
  zh-Hant : &ZH_HANT  關於
  zh-TW   : *ZH_HANT
  zh-HK   : *ZH_HANT
  ko      : &KO       소개
  ko-KR   : *KO
key: page-about
---


<head>

<meta charset="utf-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<link rel="icon" href="/favicon.png">
<title>Chenlu Miao</title>
<meta name="msapplication-tap-highlight" content="no">
<meta name="theme-color" content="#3F51B5">

<base target="_blank">

<link rel="stylesheet" href="https://cdn.bootcss.com/MaterialDesign-Webfont/2.0.46/css/materialdesignicons.min.css">
<link rel="stylesheet" href="https://cdn.bootcss.com/material-design-lite/1.3.0/material.indigo-pink.min.css">
<script src="https://cdn.bootcss.com/material-design-lite/1.3.0/material.min.js"></script>

<script>
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
})(window,document,'script','https://www.google-analytics.com/analytics.js','ga');
ga('create', 'UA-100427567-3', 'auto');
ga('send', 'pageview');
</script>
<script>
var oldOnLoad = window.onload;
window.onload = function() {
    oldOnLoad && oldOnLoad();
    var anchors = document.querySelectorAll('a');
    for (var i = 0; i < anchors.length; ++i) {
        var anchor = anchors[i];
        anchor.onclick = function() {
            window.ga && window.ga('send', 'event', 'Link', 'Click link', this.href, { transport: 'beacon' });
        }
    }
}
</script>

<link rel="stylesheet" href="index.css">
</head>

<body>
<div>

<header class="mdl-color--indigo-700 mdl-color-text--white mdl-shadow--4dp">
    <section class="title mdl-color--indigo-500">
        <h1 class="mdl-typography--display-2">Chenlu Miao</h1>
    </section>
    <section class="contact">
        <div>
            <h2 class="mdl-typography--title">Contact</h2>
            <ul>
                    <li>
                        <i class="mdi mdi-email mdi-18px"></i>
                        <a href="clmiao@zju.edu.cn">clmiao@zju.edu.cn</a>
                    </li>
                    <li>
                        <i class="mdi mdi-github-circle mdi-18px"></i>
                        <a href="https://github.com/miaochenlu">github.com/miaochenlu</a>
                    </li>
                    <li>
                        <i class="mdi mdi-link mdi-18px"></i>
                        <a href="https://miaochenlu.github.io/">https://miaochenlu.github.io</a>
                    </li>
            </ul>
        </div>
    </section>
    
</header>
<main class="mdl-color--blue-grey-50">
    <section class="mdl-color--white mdl-shadow--2dp">
        <h2 class="mdl-typography--display-1">Education</h2>
        <section>
            <h3 class="mdl-typography--title mdl-typography--title mdl-color-text--indigo-500">Zhejiang University</h3>
            <p class="mdl-typography--subhead mdl-typography--subhead-color-contrast">Bachelor of Engineering, Computer Science and Technology</p>
            <p class="mdl-typography--body-1 mdl-typography--body-1-color-contrast">
                August 2017 – Present, Hangzhou, China
            </p>
            <ul class="mdl-typography--subhead mdl-typography--subhead-color-contrast">
              	<li>Member of Pursuit Science Class (Computer Science), Chu Kochen Honors College</li>
              <li>Overall GPA: 3.98/4, 91.08/100</li>
              <ul>
                SELECTED COURSES(4.0/4.0 in all of them)
                <li>Systems: Digital Logic Design, Computer Organization, Computer Architecture, Computer Networks, Operating System</li>
                <li>Math: Mathematical Analysis, Linear Algebra, Stochastic Process, Probability and Mathematical Statistics, Applied Operation Research</li>
              </ul>
              <li>RESEARCH INTEREST: Computer Architecture and Hardware Security, recent projects focused on hardware transactional memory.</li>
            </ul>
        </section>
    </section>

</main>
</div>

<footer>
</footer>
</body>
</html>


