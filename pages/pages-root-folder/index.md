---
#
# Use the widgets beneath and the content will be
# inserted automagically in the webpage. To make
# this work, you have to use › layout: frontpage
#
layout: frontpage
header:
  image_fullwidth: header_unsplash_12.jpg
widget1:
  title: "Software Engineer"
  url: 'https://www.linkedin.com/in/wenhaozhou1802/'
  image: widget-1-302x182.jpg
  text: ' • Software Engineer Intern </br>@ <em>Techsmith</em>
  </br><div>• MS in Computer Science</div><div> @ <em>Boston University 2024</em></div></br>
  <div>• BS in Information & Computing Science </div> <div> @ <em>University of Liverpool 2022</em></div>'
  more: Linkedin >
widget2:
  title: "Musician"
  url: 'https://y.qq.com/n/ryqq/singer/004EZLJs4Rf0RB'
  text: 'Composer, Singer, Guitarist, Saxophonist.<br/>• Saxophone Principal </br> @ <em>Symphony Orchestra of XJTLU</em> <br/></p>
  • Saxophonist </br> @ <em>Concert Band of BU</em><br/></p>• Independent Musician </br> @ Tencent Music & Netease Music </br>'
  video: '<a href="#" data-reveal-id="videoModal"><img src="https://ass5bucket.s3.amazonaws.com/Music.jpg" width="302" height="182" alt=""/></a>'
  more: Original Musics >
widget3:
  title: "Traveller"
  url: 'https://www.instagram.com/danny_hao529/'
  image: gallery-example-3.jpg
  text: '<em>Always on the road</em></br> • Never stop my pace  </br>
  • Photographer </br> </p>• Always explore the beauty of nature </br>'
  more: Instagram >
#
# Use the call for action to show a button on the frontpage
#
# To make internal links, just use a permalink like this
# url: /getting-started/
#
# To style the button in different colors, use no value
# to use the main color or success, alert or secondary.
# To change colors see sass/_01_settings_colors.scss
#
callforaction:
  url: https://ass5bucket.s3.amazonaws.com/Resume_Wenhao_Zhou_2024_SDE.pdf
  text: My Tech Resume ›
  style: alert

call:
  url: https://ass5bucket.s3.amazonaws.com/music_resume.pdf
  text: My Music Resume ›
  style: alert

permalink: /index.html
#
# This is a nasty hack to make the navigation highlight
# this page as active in the topbar navigation
#
homepage: true
---
asdads
<div id="videoModal" class="reveal-modal large" data-reveal="">
  <div class="flex-video widescreen vimeo" style="display: block;">
    <iframe width="1280" height="720" src="https://space.bilibili.com/77015722?spm_id_from=333.999.0.0" frameborder="0" allowfullscreen></iframe>
  </div>
  <a class="close-reveal-modal">&#215;</a>
</div>
asdasd