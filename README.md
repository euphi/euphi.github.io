# euphi.github.io
Blog

{% for post in site.posts %}	
###({{ post.url }}){{ post.title }}
**{{ post.date | date: "%B %e, %Y" }}** . {{ post.category }} . ({{ post.url }})**  
{% endfor %}	
