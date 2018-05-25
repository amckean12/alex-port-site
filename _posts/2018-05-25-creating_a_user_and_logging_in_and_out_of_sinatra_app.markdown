---
layout: post
title:      "Creating a user and Logging in and Out of Sinatra App"
date:       2018-05-25 15:38:01 +0000
permalink:  creating_a_user_and_logging_in_and_out_of_sinatra_app
---


In my first attempt at my own CRUD MVC application I decided to use Sinatra and ActiveRecord to create a cookbook. For this application I would use everything that I have learned so far. Things included are HTML, CSS, Ruby, ActiveRecord, Sinatra framwork, gems, bundler, etc. Before starting this project it was important for me to identify the "user story" for this app. Although this would be a very simple application it is important to identify every possible path a user can take from entering the app to leaving. I've learned that developing a very detailed user story can greatly aid in the devleopment of an app specifically when it comes to the controllers. 

After creating my user story my first task was to get from the start of the path to the end. This meant creating a simple user log in and a log out function. Although this may seem simple initially there is alot that must go into this. The steps I took to create this path of login and logout are as follows. 

**Log in Log Out Steps**
1. Identify the Database that I would be using
2. Create the specific migration needed to store a user 
3. Create a user model that would hold all code in relation to a user
4. create the log in and log out controller functions
5. Create specifically the log in view and terminate a session when logging out


For this application I would be using a sqlite3 with ActiveRecord. My first task is to create the table that would store all the pertinent user information. This means username, email for validation, and a secure password. My migration for this is shown below. 

```
class CreateUsers < ActiveRecord::Migration[5.2]
  def change
    create_table :users do |t|
      t.string :username
      t.string :email
      t.string :password_digest
    end
  end
end

```

An important and vital feature when it comes to any application that stores user data, is to have a secure password. To ensure user access integrity I used the ruby gem bcrypt to encrypt my password in my database. After the developing the migration I wanted to create the routes that would get my user into and out of  the application. This is shown in the code below. 

```
  get '/login' do
    if logged_in?
       redirect to '/user_home'
    else
       erb :'/users/login'
    end
  end
	
	  post '/login' do
    user = User.find_by(:username => params[:username])

    if user && user.authenticate(params[:password])
      session[:user_id] = user.id
      redirect '/user_home'
    else
      flash[:login_error] = "Username or Password Incorrect"
      redirect '/login'
    end
  end

  get '/logout' do
    if logged_in?
      session.clear
    end
    redirect to '/'
  end
```

This code block contains two get requests and a post request as well as a helper method defined in what would become my application controller. What this code block is doing is verifying that I am who I say I am and completing the log in and log out tasks. After creating the controller actions the views where the next thing for me to do. The view for my home screen to log in are shown below. 

```
<html>
<head>
  <title>Simple Meal Prep</title>
  <link href="https://fonts.googleapis.com/css?family=Caveat" rel="stylesheet">
  <link rel="stylesheet" type="text/css" href="/stylesheet.css" />
  </head>
  <body>
    <h1 id="landing_page_title">Welcome to Cookbook</h1>
    <h2 id="site_logline">A Simple App to keep track of your recipes</h2>
    <br></br>
    <a id="login_button" href="/login">Login</a>
    <br></br>
    <a id="signup_button" href="/signup">Sign Up</a>
  </body>


</html>

```

As can be seen my login anchor is able to direct itself to the corresponding login get request to render what would be a log in form. Once a user was logged in the user would have the ability to logout via a logout form within the application. This is shown below. 

```
<h4 class="home_logout_button"><a href="/logout">Log Out</a></h4>
```

Clicking this button would terminate the session. As can be seen alot must go into a simple route such as logging in and logging out. Things to consider for this route are: 

* How will you store user data
* How will you encrypt sensitive user data (i.e. passwords, credit cards, chats, etc.) 
* How will you verifiy that the user is who they say they are

Logging in and out may in fact be a simple function to do, but it is the basis of the integrity of every web applcation using multiple users. It is important to get this function right. 

