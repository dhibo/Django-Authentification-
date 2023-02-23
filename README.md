"I am developing an authentication microservice that utilizes the powerful auth_models from the Django framework. To ensure secure storage of user data, I have chosen to integrate a PostgreSQL database. For streamlined user registration and login processes, I am incorporating the Knox library."
and this a few steps to make your own login and register microservice :)


1 - create virtual environement using 
    python3 -m venv yourvritualenv

2- then activate your virtual environement with 
    source virtualenvname/bin/activate  ( using terminal bash )

3 - install django 
    pip install django 

4 - install django rest framework 
    pip install djangorestframework

5 - create your project
    django-admin startproject name .

6 - run the server ( for testing )
    python3 manage.py runserver 

7 - create your application 
    python3 manage.py startapp name 

8 - add to your settings.py in your project folder 
    your application 'app_name'
    restframework 'rest_framework' 
    add knox ( library that we gonna to use )

9 - create file in your application folder "serializers.py" and add this code : 
    from rest_framework import serializers
    from django.contrib.auth.models import User

    # User Serializer
    class UserSerializer(serializers.ModelSerializer):
        class Meta:
            model = User
            fields = ('id', 'username', 'email')

    # Register Serializer
    class RegisterSerializer(serializers.ModelSerializer):
        class Meta:
            model = User
            fields = ('id', 'username', 'email', 'password')
            extra_kwargs = {'password': {'write_only': True}}

        def create(self, validated_data):
            user = User.objects.create_user(validated_data['username'], validated_data['email'], validated_data['password'])

            return user

10 - then add urls.py to the same folder and add this code : 
    from .views import RegisterAPI
    from django.urls import path
    from .views import LoginAPI
    from knox import views as knox_views

    urlpatterns = [
        path('login/', LoginAPI.as_view(), name='login'),
        path('logout/', knox_views.LogoutView.as_view(), name='logout'),
        path('logoutall/', knox_views.LogoutAllView.as_view(), name='logoutall'),
    ]

11 - now add to urls.py in project folder this code : 

    from django.contrib import admin
    from django.urls import path 
    from django.urls import include
    from authentif.views import RegisterAPI
    from rest_framework_swagger.views import get_swagger_view

    schema_view = get_swagger_view(title='Pastebin API')


    urlpatterns = [
        path('admin/', admin.site.urls),
        path('api/register/', RegisterAPI.as_view(), name='register'),
        path('api/',include('authentif.urls')),
        path(r'^$', schema_view),
    ]


12 - for now if you want to use sqlite you can just run the server ( python3 manage.py runserver )

13 - if you want to add your postgresSQL 
    first : check if postgresSQL installed in your machine 
    second : make sure to install ' pip install psycopg2 ' for windows and for linux ' pip install psycopg2-binary '
    third : go to the postgres in your terminal and create database and role then grant privileges to create , update and delete 
    in the end go to your databases in your settings.py and add your own credentials like this : 
    DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'userauth', 
        'USER': 'admin', 
        'PASSWORD': 'admin',
        'HOST': '127.0.0.1', 
        'PORT': '5432',
        }
    }
    now run this two to commands to create model in your database :
        pyton3 manage.py makemigrations 
        pyton3 manage.py migrate
    and then run your server  