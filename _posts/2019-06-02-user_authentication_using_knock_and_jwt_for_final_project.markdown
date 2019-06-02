---
layout: post
title:      "User Authentication Using Knock and JWT For Final Project"
date:       2019-06-02 16:28:09 +0000
permalink:  user_authentication_using_knock_and_jwt_for_final_project
---


This was without a doubt one of the more difficult portions of my project. Although user authentication isn't necessarily required for the Flatiron final project I thought it would be a great idea to try implement. User authenitcation really encompases a lot of topics in full stack development. Through implmenting this I was able to gain a better understanding of data really moves throughout an application. This blog will give an overview of what I did to solve this problem on the backend. 

For me, when I began my final project I started with the creation of the user. This meant everything needed to get a user from the homepage to logged into a profile and stored on the system. The first step I took to accomplish this was set up the backend. 

Since I'm using Rails API there were two gems that I found to be extrmely useful. The gems were JWT and knock. JWT is a JSON web token that's going to act as "key" allowing me to authenticate the user when they hit certain controllers in my api. Knock is the gem that has the methods to authenicate this key. The reason I chose knock is because it was built with rails api and jwt authentication in mind. Alot of students I talked to were using devise as an authentication engine, but for me Devise was too heavy of a gem for a rails API. Knock has very good documentation on how to set up and implment for the api. I was able to follow the readme and get up and authenticating really quickly. One thing not in the readme that you need to be included if using Rails 5.2 is... 

1. A skip_before_action:verify_authenticity_token in a user token controller. If this is not inluded you will get a 422 response. 

```
class Api::UserTokenController < Knock::AuthTokenController
  #Solves 422 issue with rails 5.2 and Knock
  skip_before_action :verify_authenticity_token
end

```


Following the documentation and putting a before_action :authenticate_user in each controller will allow you to lock down your backend and protect your data.

