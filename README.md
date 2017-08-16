# euphi.github.io
Blog

{% for post in site.posts %}	
    ###<a href="{{ post.url }}">{{ post.title }}</a></h3>
    **{{ post.date | date: "%B %e, %Y" }}** . {{ post.category }} . ({{ post.url }})**
    
{% endfor %}	
