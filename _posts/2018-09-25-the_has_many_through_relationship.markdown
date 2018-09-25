---
layout: post
title:      "The has_many through relationship"
date:       2018-09-25 12:56:14 +0000
permalink:  the_has_many_through_relationship
---


Lets say you are building a magazine subscription site that is going to allow the user to subscribe to multiple magazines. How would you set this up? We know that a magazine is going to belong to a user, but is it just one user? No, that wouldnt make any sense. Multiple users are going to want to be able to read the same magazine. So how do we set up a relationship where a user can have many magazines while a magazine can have many users. We can set this up using a has_may through relationship. 

What a has many through relationship does is it says that one model is associated with many instances of another model through a third model. This third model is a joins table. The responsibility of a joins table in Activerecord is to "join" two models together. 

In our magazine example we have a user model and a magazine model. We want to join these two models through a third model we'll call subscriptions. In this example, we would say that a User has many Magazines through Subscriptions. While a Magazine has many users through subscriptions. This relationships helps set up this complex relationship between the two models and is very useful when creating a rails application. 
