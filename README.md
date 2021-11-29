# Live Project - Django
# Introduction
I worked on a live project over a two week sprint building an app using the Django Framework as part of The Tech Academy. My app was part of a larger project called AppBuilder9000. We used Azure DevOps and Discord to stay in communication and for Agile/Scrum meetings. 
## Creating My App (User Story 1)
I created my app Hot Springs and registered my app within the main project. I created base and home templates, which included block tags. I added a function to views to render the homepage, and registered my urls with the main app and created a urls.py file for my app and homepage. Lastly, I linked my apps homepage to the projects homepage with an image link.
#### Registering My App
```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'bootstrap4',
    'crispy_forms',
    'HotSprings',
]
```
#### Base Template
```
{% load static from staticfiles %}
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>{% block title %}{% endblock %}</title>
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link rel="stylesheet" type="text/css" href="/static/css/hotsprings.css">
    </head>
<body>
    <div class="page_container">
       {% include "hotsprings_navbar.html" %}
        <header class="header-title">
            <h1>{% block header %}{% endblock %}</h1>
        </header>
        {% block content %}{% endblock %}
    </div>

    {% include "footer.html" %}
</body>
</html>
```
#### Home Template
```
{% extends "hotsprings_base.html" %}

{% block title %}Hot Springs{% endblock %}

{% block header %}Find Your New Favorite Hot Springs!{% endblock %}

{% block content %}


{% endblock %}
```
#### Function to Views
```
def hotsprings_home(request):
    # render method takes the request object and template name as arguments
    # returns httpResponse object with rendered text.
    return render(request, 'hotsprings_home.html')
```
#### Register URL with MainApp
```
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.home, name='home'),
    path('HotSprings/', include('HotSprings.urls')),
]
```
#### URLS.py
```
from django.urls import path
from .import views


urlpatterns = [
    path('', views.hotsprings_home, name='hotsprings_home'),
]
```
#### Image Link
```
              <!-- HotSprings link -->
              <a href="{% url 'hotsprings_home' %}" class="grid-item app-image">
              <img src="{% static 'img/gold.jpg' %}" alt="Hot Springs">
              <div class="fadedbox">
                  <div class="title text">Hot Springs</div>
              </div>
              </a>
              <!-- HotSprings link -->
```
## Create My Model (User Story 2)
I created my model to track Hot Springs and the ability to create a new Hot Springs. I included an objects manager for accessing the database. I created a model form that includes inputs the user needs to make. I added a template for creating a new Hot Springs. I then added a function to views to render the create page and utilizes model form to save the informtion to the database. 
#### Model
```
from django.db import models

type_choices = [
    ('Outdoor', 'Outdoor'),
    ('Indoor', 'Indoor'),
    ('Both', 'Both'),
]

bathingsuit_choices = [
    ('Required', 'Required'),
    ('Optional', 'Optional'),
]

stay_overnight = [
    ('Tent Camping', 'Tent Camping'),
    ('Trailer Hookups', 'Trailer Hookups'),
    ('Cabin Rentals', 'Cabin Rentals'),
    ('Resort', 'Resort'),
    ('Camping & Rentals', 'Camping & Rentals'),
    ('No Overnight Stay', 'No Overnight Stay'),
]

class HotSprings(models.Model):
    hot_springs_name = models.CharField(max_length=50)
    hot_springs_type = models.CharField(max_length=60, choices=type_choices)
    clothing_optional = models.CharField(max_length=60, choices=bathingsuit_choices)
    stay_accommodations = models.CharField(max_length=60, default="", choices=stay_overnight)
    description = models.TextField(max_length=500, default="", blank=True)


class Location(HotSprings):
    city = models.CharField(max_length=50)
    state = models.CharField(max_length=50)

    objects = models.Manager()

    def __str__(self):
        return self.hot_springs_name
```
#### Model Form
```
from django.forms import ModelForm
from .models import HotSprings, Location

class hotspringsForm(ModelForm):
    class Meta:
        model = HotSprings
        model = Location
        fields = '__all__'
```
#### Create Template
```
{% load static %}

{% include "hotsprings_navbar.html" %}

{% block content %}

    <h1 class="heading">Add a New Hot Springs to Our List:</h1>

        <div class="form-container">
        <form method="POST">
            {% csrf_token %}
            {{ form.as_p }}
            <div class="frmBtn_container">
                <input type="submit" class="btn" id="btn" value="Add Hot Springs" name="Add_HotSprings">
                <a href="{% url 'hotsprings_home' %}"><button type="button" class="btn" id="btn" value="Cancel">Cancel</button></a>
            </div>
        </form>
    </div>

{% endblock %}
```
#### Function to Views
```
def add_hotsprings(request):
    form = hotspringsForm(data=request.POST or None)
    if form.is_valid():
        form.save()
        return redirect('hotsprings_list')
    else:
        print(form.errors)
        form = hotspringsForm()
        context = {'form' : form}
    return render(request, 'hotsprings_create.html', context)
```
#### URL Path
```
from django.urls import path
from .import views


urlpatterns = [
    path('', views.hotsprings_home, name='hotsprings_home'),
    path('hotsprings_create/', views.add_hotsprings, name='hotsprings_create'),

]
```
## Display All Items From Database (User Story 3)
I added an HTML page that lists all Hot Springs in the database and linked it to my homepage. I added a function that gets all Hot Springs from the database and sends them to the template. Then displays the Hot Springs name and description. 
#### Hot Springs Nav Bar
```
 <ul class="topnav">
            <li><a href="{% url 'hotsprings_home' %}">Home</a></li>
            <li><a href="{% url 'hotsprings_list' %}">Hot Springs List</a></li>
 </ul>
```
#### Views Function
```
def list_hotsprings(request):
    hotsprings = HotSprings.objects.all()
    return render(request, 'hotsprings_list.html', {'hotsprings': hotsprings})
```
#### URL Path
```
from django.urls import path
from .import views


urlpatterns = [
    path('', views.hotsprings_home, name='hotsprings_home'),
    path('hotsprings_create/', views.add_hotsprings, name='hotsprings_create'),
    path('hotsprings_list/', views.list_hotsprings, name='hotsprings_list'),
]
```
#### Hot Springs List
```

{% block header %}Hot Springs{% endblock %}

{% include "hotsprings_navbar.html" %}

{% block content %}

<div class="admin_panel">
    <form name="hotspringsForm" method="POST" action="">
        <div class="formObject">
            {% csrf_token %}
            <label for="menu_id">Select a hot springs from the list to edit:</label>
            <select id="menu_id" name="menu" onChange="top.location.href=this.options[this.selectedIndex].value;">
            {% for hotsprings in hotsprings %}
                <option value="../{{ hotsprings.pk }}/hotsprings_details/">{{ hotsprings.hot_springs_name }}</option>
            {% endfor %}
            </select>
            <div class="fromBtn_container">
                <a href="{% url 'hotsprings_create' %}"><button type="button" class="btn">Add New Hot Springs</button></a>
                <a href="{% url 'hotsprings_home' %}"><button type="button" class="btn">Return Home</button></a>
            </div>

        </div>
    </form>
</div>



<div class="main">

	{% for hotsprings in hotsprings %}

	<a href = "../{{ hotsprings.pk }}/hotsprings_details/">{{ hotsprings.hot_springs_name }}<br/>
	{{ hotsprings.description}}<br/>
	<hr/>

	{% endfor %}

</div>

{% endblock %}
```
## Details Page
I created a details page that will display all the information for a single Hot Springs from within the database as selected by the user. I created a link for each Hot Springs in order to view the details of that Hot Springs. 
####  Details Template
```
{% block header %}Hot Springs{% endblock %}

{% include "hotsprings_navbar.html" %}

{% load static %}
{% block title %}Hot Springs Details{% endblock %}

{% block content %}
<div class="main">

	<!-- Specify fields to be displayed -->
	{{ details.hot_springs_name }}<br/>
	{{ details.description }}<br/>
	{{ details.hot_springs_type }}<br/>
	{{ details.clothing_optional }}<br/>
	{{ details.stay_accommodations }}<br/>
	{{ details.city }}<br/>
	{{ details.state }}<br/>

</div>
{% endblock %}
```
#### URL Path
```
from django.urls import path
from .import views


urlpatterns = [
    path('', views.hotsprings_home, name='hotsprings_home'),
    path('hotsprings_create/', views.add_hotsprings, name='hotsprings_create'),
    path('hotsprings_list/', views.list_hotsprings, name='hotsprings_list'),
    path('<int:pk>/hotsprings_details/', views.hotsprings_details, name='hotsprings_details'),

]
```
#### Views Function
```
def hotsprings_details(request, pk):
    details = get_object_or_404(HotSprings, pk=pk)
    context = {'details': details}
    return render(request, 'hotsprings_details.html', context)
```
#### Added Links
Finished code can be seen above in Hot Springs List!
