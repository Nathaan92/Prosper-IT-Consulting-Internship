# Live Project

## Introduction
Over the course of the last two weeks I have partaken in the tech Academys Live Project working part time.
I have worked with my peers in a team environment developing apps using Django framework. Over the course of the project I started the development of my app known as HAPP 
and was able to complete four stories over the course of a full work week. In my opinion this project allowed me insight into the day to day of working in a development team 
and the process by which work is completed in this environment. By working on the development of an app I was able to work on both [Front End](#Front-End) and [Back End](#Back-End) 
tasks which was an excellent opportunity to try out the skills that I had developed during my boot camp course. All were of varying degrees of difficulty and required me to use my 
project management and team working skills which I'm confident I will use again and again on future projects.
  
Below are descriptions of the stories I worked on, along with code snippets and navigation links.



### Back End
* [Models](#Models)
* [Forms](#Forms)
* [Views](#Views)
* [URLs](#URLs)

## Models

I created 2 models for this app. One that is a survey that the users can fill 
out regarding thier preferences and the second that ceates a new user.
from django.db import models


	class create_happ_user(models.Model):
		# default manager (object) changed to admin
    		admin = models.Manager()

    		# fields = field types(field options)
    		first_name = models.CharField(max_length=30, blank=False)
    		last_name = models.CharField(max_length=30, blank=False)
    		# primary key
    		nick_name = models.CharField(primary_key=True, max_length=30, blank=False)
    		street_address = models.CharField(max_length=30, blank=True)
    		city = models.CharField(max_length=30, blank=True)
    		state = models.CharField(max_length=30, blank=False)
    		country = models.CharField(max_length=30, blank=True)
    		email = models.EmailField(max_length=30, blank=False)
    		password = models.CharField(max_length=30, blank=False)

    		def __str__(self):
        		return self.nick_name


		# choices available for model hiking db hike_preferences
		hike_type = [
    		('Flat', 'Flat'),
    		('Hilly', 'Hilly'),
    		('Mountains', 'Mountains'),
    		('Rivers', 'Rivers'),
    		('Glacier', 'Glacier'),
    		('Lake', 'Lake')
		]

		hike_length = [
    		('1-2 Miles', '1-2 Miles'),
    		('2-5 Miles,', '2-5 Miles'),
    		('5-8 Miles', '5-8 Miles'),
    		('8-10 Miles', '8-10 Miles'),
    		('10+ Miles', '10+ Miles')
		]

		healthy = [
    		('Not to Healthy', 'Not to Healthy'),
    		('Somewhat Healthy', 'Somewhat Healthy'),
    		('Healthy', 'Healthy'),
    		('Pretty Healthy', 'Pretty Healthy'),
    		('Very Healthy', 'Very Healthy')
		]

		avid = [
    		(True, 'Yes'),
    		(False, 'No')
		]


		class hike_preferences(models.Model):
    		# default manager (object) changed to admin
    		admin = models.Manager()

    		# fields = field types(field options)
    		# One To One Field relationship with Primary Key
    		nick_name = models.OneToOneField(create_happ_user, on_delete=models.CASCADE)
    		favorite_types_of_hikes = models.CharField(max_length=30, choices=hike_type, blank=False)
    		perfect_length_of_hike = models.CharField(max_length=30, choices=hike_length, blank=False)
    		avid_hiker = models.BooleanField(choices=avid, default=False)
    		how_healthy_are_you = models.CharField(max_length=30, choices=healthy, blank=False)

    		def __str__(self):
       			return self.nick_name

## Forms

To provid the users with a actionable form that they can fill out.

		from django.forms import ModelForm
		from .models import create_happ_user, hike_preferences
		
		class create_new_happ_user(ModelForm):
    			class Meta:
        		# selected model is create_happ_user
        		model = create_happ_user
        		# model fields to be include in form is all
        		fields = '__all__'

		class survey(ModelForm):
    		class Meta:
        		# selected model is hike_preferences
        		model = hike_preferences
        		# model fields to be include in form is all
        		fields = '__all__'

## Views 

I created 5 functions for my app. The Home function will return the user to the home page. Details renders a web page that provides details regarding a selected field in the database,
Surevey Results renders a page that provides all fields from the specified database. Daily survey renders a survey webpage that a user can fill out and submit. Finally create happ user will
create a new happ user that is saved to the data base. 


		from django.shortcuts import render, redirect, get_object_or_404
		from .forms import create_new_happ_user, survey
		from .models import hike_preferences

		def home(request):
    		# returns home page
    		return render(request, 'HikingApp/HikingApp_Home.html')

		def details(request, pk):
    		# detail is = to selected data
    		detail = get_object_or_404(hike_preferences, pk=pk)
    		# content is = to the dictionary value and object
    		content = {'detail': detail}
    		# returns detail page and selected db content
    		return render(request, 'HikingApp/HikingApp_Details.html', content)

		def survey_result(request):
    		# survey_results is = to all data in db
    		survey_results = hike_preferences.admin.all()
    		# content is equal to dictionary value and object
    		content = {'survey_results': survey_results, }
    		# returns survey results page and all db content
    		return render(request, 'HikingApp/HikingApp_SurveyResults.html', content)


		def daily_survey(request):
    		# form is = to form survey and will post to the db
    		form = survey(data=request.POST or None)
    		# if the method is post
    		if request.method == 'POST':
        		# if the form is valid
        		if form.is_valid():
            		# save form
            		form.save()
        		# redirect to happ home
        		return redirect('happ_home')
    		# renders the Survey page and form
    		return render(request, 'HikingApp/HikingApp_Survey.html', {'form': form})

		def create_the_happ_user(request):
    		# form is = to form create new happ user and will post to db
    		form = create_new_happ_user(data=request.POST or None)
    		# if the method is post
    		if request.method == 'POST':
        		# if the form is valid
        		if form.is_valid():
            		# save form
            		form.save()
            		# redirect to happ_home
            		return redirect('happ_home')
    		# renders the MyProfile page and form
    		return render(request, 'HikingApp/HikingApp_MyProfile.html', {'form': form})

## URLs 

This page maps the fucntions created prior to the URL

		from django.urls import path
		from .import views

		from django.urls import path
		from .import views

		urlpatterns = [
    		# URL pattern to home page , views function home , named happ_home
    		path('', views.home, name='happ_home'),

    		# URL pattern to my _profile page , views function create_the_happ_user , named happ_my_profile
    		path('my_profile/', views.create_the_happ_user, name='happ_my_profile'),

    		# URL pattern to survey page, views function daily_survey , named happ_survey
    		path('survey/', views.daily_survey, name='happ_survey'),

    		# URL pattern to survey results page , views function survey_result , named survey_result
    		path('survey_results/', views.survey_result, name='survey_results'),

    		# URL pattern details page , views function details , named survey_results_details
    		path('details/<int:pk>', views.details, name='survey_results_details'),

		]


*Jump to: [Back End](#Back-End), [Front End](#Front-End)*


### Front End
* [Base](#Base)
* [Home Page](#Home-Page)
* [Nav Bar](#Nav-Bar)
* [Survey](#Survey)
* [Survey Results](#Survey-Results)
* [CSS](#CSS)

This is a collection of HTML pages, their respective base, page elements, and CSS styling. Rather than going with a frame work I decided
to create a website on my own initiative. 

## Base

		{% load static %}
		<!DOCTYPE html>
		<html lang="en">
		<head>
    		<meta charset="UTF-8">
    		<title>{% block title %}{% endblock %}</title>
   		 <meta name="viewport" content="width=device-width, initial-scale=1">
    		<!-- link to stylesheet -->
    		<link rel="stylesheet" type="text/css" href="{% static 'CSS/HikingApp.css' %}">
		</head>

		<!-- tag for loading and rendering navbar template -->
		<nav>{% include "HikingApp/HikingApp_Navbar.html" %}</nav>

		<body>
    		<div class="page_container">
            		<header class="header_title">
                 		<!-- tag for loading and rendering html snippets provided by other template pages -->
                		<h1 class="main_title">{% block header %}{% endblock %}</h1>
            		</header>
            		<!-- tag for loading and rendering html snippets provided by other template pages -->
            		{% block contentA %}{% endblock %}
        		</div>
		</body>
		<!-- tag for loading and rendering footer template -->
		<footer>{% include "HikingApp/HikingApp_Footer.html" %}</footer>
		</html>

## Nav Bar

		{% load static %}

		<!-- Link to style sheet -->
		<link rel="stylesheet" type="text/css" href="{% static 'CSS/HikingApp.css' %}">

		<!-- primary nav -->
		<div class="primary_nav">
   		<ul>
        		<!-- nav links to various pages -->
        		<li><a href="{% url 'happ_home' %}" class="active">Home</a></li>
        		<li><a href="">Trail Search</a></li>
        		<li><a href="">Conditions</a></li>
        		<li><a href="{% url 'survey_results' %}" class="active">Survey Results</a></li>
        		<li><a href="{% url 'happ_my_profile' %}" class="active">My Profile</a></li>
    		</ul>
		</div>

## Home Page

		<!-- extending block tags from base template -->
		{% extends "HikingApp/HikingApp_Base.html" %}

		<!-- block tags associated with base template -->
		{% block title %}Happ{% endblock %}

		{% block header %}Welcome to Happ! The Hiking App!{% endblock %}


		{% block contentA %}
    		<div class="contentA_container">
       		 <!-- a clickable link to the survey page -->
        		<div class="survey_container">
            		<h2 class="sub_title-a">Would you like to fill out our poll?</h2>
            		<a href="{% url 'happ_survey' %}" class="active">Go to survey</a>
        		</div>
    	</div>
		{% endblock %}

## Survey

		<!-- extending block tags from base template -->
		{% extends "HikingApp/HikingApp_Base.html" %}

		<!-- block tags associated with base template -->
	{% block title %}Survey{% endblock %}

		{% block header %}HAPP Survey{% endblock %}

		{% block contentA %}
    		<div class="contentA_container">
        		<div class="survey_container">
           		 <h2 class="sub_title-a">Please fill out the survey below.</h2>
             		<!-- container for survey form -->
            		<div class="form_container">
                		<form method="POST">
                    		{% csrf_token %}
                    		<!-- survey form -->
                    		{{ form.as_ul }}
                    		<!-- submit button -->
                    		<button id="submit_poll" type="submit" onclick="">Submit Poll</button>
                		</form>
            		</div>
        		</div>
    		</div>
		{% endblock %}

## Survey Results

		<!-- extending block tags from base template -->
		{% extends "HikingApp/HikingApp_Base.html" %}

		<!-- block tags associated with base template -->
		{% block title %}Happ{% endblock %}

		{% block header %}Survey Results{% endblock %}
		{% block contentA %}

		<!-- link to bootstrap styling for the container class -->
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css">

		<div class="contentA_container">

    		<!-- container for current and future surveys -->
    		<div class="survey1">
        		<h2>Survey: Hiking Preferences</h2>
        		<h3>Results</h3>
        		<div class="container">
            		<!-- top horizontal row which includes survey topic headers -->
            		<div class="row">
                    		<div class="col-sm-3">Nick name</div>
                    		<div class="col-sm-3">Favorite type of hike</div>
                    		<div class="col-sm-3">Perfect length of hike</div>
                    		<div class="col-sm-3">Avid hiker</div>
            		</div>
            		<br>
            		<!-- inclusion of a border between both instances of row class -->
            		<div class="bottom_border"></div>
            		<br>
            		<!-- for loop to iterate through each result in survey_results -->
            		{% for result in survey_results %}
            		<!-- horizontal row which contain survey result data -->
                		<div class="row">
                        		<!-- columns that includes the all the values of each db field and provide a link to the details page-->
                        		<a href="{% url 'survey_results_details' result.pk %}" class="col-sm-3">{{ result.nick_name }}</a>
                        		<a href="{% url 'survey_results_details' result.pk %}" class="col-sm-3">{{ result.favorite_types_of_hikes }}</a>
                        		<a href="{% url 'survey_results_details' result.pk %}" class="col-sm-3">{{ result.perfect_length_of_hike|linebreaks }}</a>
                        		<a href="{% url 'survey_results_details' result.pk %}" class="col-sm-3">{{ result.avid_hiker|yesno:"Yes,No" }}</a>
                		</div>
            		{% endfor %}
        		</div>
    		</div>
		</div>
		{% endblock %}

## CSS

		html, body {
    		height: 100%;
			font-family: "Arial Black", Gadget, sans-serif;
			background-image: url('/static/Images/Mount-Rainier.jpg');
   		 background-size: cover;
    		background-attachment: fixed;
    		background-position: center;
    		position: relative;
		}

		.page_container {
		padding: 150px;
		text-align: center;
		margin: auto;

		}

		.main_title, .contentA_container {
   		 color: black;
    		border: solid black;
    		border-radius: 10px;
    		background-color: darkorange;
    		padding: 10px;
 		}

 		.form_container, .survey1{
    		text-align: center;
    		list-style-type: none;

 		}

 		.container {
   		 overflow: hidden;
   		 position: relative;
    		z-index: 1;
    		width: auto;
    		height: auto;
    		border: solid black;
    		border-radius: 10px;
    		margin-left: auto;
    		margin-right: auto;
    		margin-top: 25px;

		}

		.bottom_border {
 		border-bottom:solid black;
		}

		.primary_nav ul {
   		 position: fixed;
    		z-index: 2;
    		top: 0;
    		list-style-type: none;
    		margin: 0;
    		padding: 0;
    		left: 0;
    		border: solid black;
    		border-radius: 10px;
    		background-color: darkorange;
		}

		.primary_nav li{
		float: left;
   		 display: inline;
    		padding: 7px;

		}

		.primary_nav a {
		color: black;
		}

		.primary_nav li:hover {
		background-color: red;
		border-radius: 10px;
		}

		footer {
    		color: black;
    		border: solid black;
   		 border-radius: 10px;
   		 background-color: darkorange;
   		 position:fixed;
   		 z-index: 2;
   		 bottom: 0;
   		 left: 0;
   		 padding-top: 10px;
   		 padding-right: 10px;
   		 padding-bottom: 0px;
   		 padding-left: 10px;

		}

*Jump to: [Back End](#Back-End), [Front End](#Front-End)*





