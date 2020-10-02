# Django Review

##### Summarized from
* [ sf-sei-5/django-models ](https://git.generalassemb.ly/sf-sei-5/django-models)
* [ sf-sei-5/django-views-and-templates ](https://git.generalassemb.ly/sf-sei-5/django-views-and-templates)

### Getting Started
* ##### Create Project Directory
```
    mkdir <project name>
    cd  <project name>
```
* ##### Create Virtual Environment
```
    pip3 install vitualenv // via vitualenv
    virtualenv .env -p python3
    
    python3 -m venv .env // via python3

      <!-- start env -->
    source .env/bin/activate
      <!-- stop env -->
          deactivate
```

 * ##### Install Dependencies
```
    pip3 install Django
    pip3 install psycopg2
    pip3 install django-extensions
    pip3 freeze > requirements.txt

    <!--  keeps track of dependencies -->
    pip3 install -r requirements.txt

```
 * ##### Start Project, Create App, Create DB
```
    django-admin startproject <projectname> .
    django-admin startapp <appname> or python3 manage.py startapp main_app
```

```
    psql postgres
    CREATE DATABASE <db name>
```
* #### Working w/ postgres, updating <settings.py>
    * In the projectname/settings.py - in the DATABASE dictionary
```
    => usually line 76

    DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'projectname',
        }
    }
```
* #### Now, update INSTALLED_APPS in the same file
```
    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'projectname'
        'django_extensions',
    ]
```
### Start Server
```
    python3 manage.py runserver
```
* check [localhost:8000](http://localhost:8000/) in browser

* #### Setting Up Models
    * in appname/models.py
    model example
```
    class Artist(models.Model):
        name = models.CharField(max_length=100)
        nationality = models.CharField(max_length=100)
        photo_url = models.TextField()


        def __str__(self):
            return self.name


    modal 2 referencing between 2 models
    class Song(models.Model):
        artist = models.ForeignKey(Artist, on_delete=models.CASCADE, related_name='songs')
```
* #### Migrations ( do this after creating a Model )
```
    python3 manage.py makemigrations
    python3 manage.py migrate
```    
* #### Admin Console & Setup
```
    python3 manage.py createsuperuser
```
* in appname/admin.py
```
    from django.contrib import admin
    from .models import Artist
    admin.site.register(Artist)
```
* #### Run Server & Check Admin Dash
```
    python3 manage.py runserver
```
* check [localhost:8000/admin](http://localhost:8000/admin) in browser


* #### Django ORM, Shell Access
```
    python3 manage.py shell
    quit()
```
* #### < APP_NAME > Views 
    * appname/views.py - SHOW ALL / ONE
```
   # NOTE INDEX View
    def artist_list(request):
        artists = Artirt.objects.all()
        return render(request, 'tunr/artist_list.html', {'artists': artists})


    # NOTE SHOW View
    def artist_detail(request, pk):
        artist = Artirt.objects.get( id=pk )
        return render(request, 'tunr/artist_detail.html', {'artist': artist} )  
```    
* #### < APP_NAME > URL
    * appname/url.py - (u will need to create the file)
```
    from new_review_app.views import artist_detail
    from os import name
    from django.urls import path
    # from django.contrib.auth.models import User
    from . import views
    # from django.conf import settings



    urlpatterns = [
        path( 'artists', views.artist_list, name='artist_list' ),
        path('artists/<int:pk>', views.artist_detail, name='artist_detail')
    ]
```    

* #### < PROJECT_NAME > URL
    * projectname/urls.py
```
    from django.conf.urls import include
    from django.urls import path
    from django.contrib import admin


    urlpatterns = [
        path('admin', admin.site.urls),
        path('', include('tunr.urls')),
    ]
```

* #### Template - Create HTML file for the URLs and Views you just set up
    * Create appname/templates/artist_list.html - SHOW ALL
    * Create appname/templates/artist_detail.html - SHOW ONE
```
    <!-- templates/artist_list.html -->

    <h1>Artists</h1>
    <ul>
        {% for artist in artists %}
            <li>
                <a href="{% url 'artist_detail' pk=artist.id %}">{{ artist.name }}</a>
            </li>
        {% endfor %}
    </ul>
```  
```
    <!-- templates/artist_detail.html -->

    {% extends 'base.html' %} uses the base.html

    {% block content %}
    <main>
        <h1>Artist Details</h1>
        
        <h2>{{ artist.name }}</h2>
    </main>
    {% endblock %}
```
* #### Base HTML
    * templates/appname/base.html
```
    {% load static %}
    
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">

        <title>Tunr</title>
    </head>
    <body>
        <nav>
            <a href="/">Home</a>
            <a href="{% url 'artist_list' %}">Artists</a>
        </nav>
        
        {% block content %}
        {% endblock %}
    </body>
    </html>
```


* #### Forms - CREATE
    * Create appname/forms.py
    * Update appname/views.py with the create
```
    from django import forms
    from .models import Artirt

    class ArtistForm( forms.ModelForm ):
        class Meta:
            model = Artirt
            fields = ( 'name', ) # we most leave a , because is expenting a tuple
```

```
    def artist_create(request):
        if request.method == 'POST':
            form = ArtistForm(request.POST)
            if form.is_valid():
                artist = form.save()
                return redirect('artist_detail', pk=artist.pk)
        else:
            form = ArtistForm()
        return render(request, 'tunr/artist_form.html', {'form': 
```

* #### Create Template
    * appname/templates/appname/artist_form.html
```
    {% extends 'base.html' %}

    {% block content %}
        <h1>New Artist</h1>
        <form method="POST" class="artist-form">
            {% csrf_token %}
            {{ form.as_p }}
            <button type="submit" class="btn">Save</button>
        </form>
    {% endblock %}
```    

* #### Edit Template
    * update appname/views.py
    * update appname/urls.py
```
    def artist_edit(request, pk):
        artist = Artirt.objects.get(pk=pk)
        if request.method == "POST":
            form = ArtistForm(request.POST, instance=artist)
            if form.is_valid():
                artist = form.save()
                return redirect('artist_detail', pk=artist.pk)
        else:
            form = ArtistForm(instance=artist)
        return render(request, 'artist_form.html', {'form': form})
```    

* #### Delete - DESTROY
    * update appname/views.py
    * update appname/urls.py
```    
    def artist_delete(request, pk):
        Artist.objects.get(id=pk).delete()
        return redirect('artist_list')
```    

```
<!-- appname/urls.py -->
path('artists/<int:pk>/delete', views.artist_delete, name='artist_delete'),
```

### Full CRUD in one resorse
* appname/views.py should look something like this
```
    from django.http import request
    from django.shortcuts import render, redirect
    from .models import Artirt, Song
    from .forms import ArtistForm

    # NOTE INDEX View
    def home( request ):
        return render( request, 'home.html') 

    def artist_list( request ):
        artists = Artirt.objects.all()
        return render(request, 'artist_list.html', {'artists': artists})


    # NOTE SHOW View
    def artist_detail( request, pk ):
        artist = Artirt.objects.get( id=pk )
        return render(request, 'artist_detail.html', {'artist': artist} ) 


    # NOTE Create View
    def artist_create( request ):
        if request.method == 'POST':
            form = ArtistForm( request.POST )

            if form.is_valid():
                artist = form.save()
                return redirect( 'artist_detail', pk= artist.id )
        else:
            form = ArtistForm()
        return render(request, 'artist_form.html', { 'form': form })


    # NOTE Update View
    def artist_edit(request, pk):
        artist = Artirt.objects.get(pk=pk)
        if request.method == "POST":
            form = ArtistForm(request.POST, instance=artist)
            if form.is_valid():
                artist = form.save()
                return redirect('artist_detail', pk=artist.pk)
        else:
            form = ArtistForm(instance=artist)
        return render(request, 'artist_form.html', {'form': form})


    # NOTE Destroy View
    def artist_delete( request, pk ):
        Artirt.objects.get(id=pk).delete()
        return redirect('artist_list')
        
```

* appname/url.py should look something like this

```
    from os import name
    from django.urls import path
    # from django.contrib.auth.models import User
    from . import views
    # from django.conf import settings



    urlpatterns = [
        path( '', views.home, name='home' ),
        path( 'artists', views.artist_list, name='artist_list' ),
        path( 'artists/<int:pk>', views.artist_detail, name='artist_detail' ),
        path( 'artists/new', views.artist_create, name='artist_create' ),
        path('artists/<int:pk>/edit', views.artist_edit, name='artist_edit'),
        path('artists/<int:pk>/delete', views.artist_delete, name='artist_delete')
    ]
```

...to be contined
