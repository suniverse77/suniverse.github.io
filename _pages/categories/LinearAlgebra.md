 ---
  title: "인공지능 수학 / 선형대수"
  layout: archive
  permalink: categories/인공지능 수학/선형대수/
  author_profile: true
  sidebar_main: true
  ---
  
  {% assign posts = site.tags['선형대수'] %}
  {% for post in posts %} 
    {% include archive-single.html type=page.entries_layout %}
  {% endfor %}
