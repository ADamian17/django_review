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
    pip3 install vitualenv
    virtualenv .env -p python3

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
    django-admin startapp <appname>
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
    
