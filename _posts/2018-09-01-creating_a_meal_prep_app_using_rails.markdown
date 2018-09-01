---
layout: post
title:      "Creating a Meal Prep App Using Rails"
date:       2018-09-01 20:40:50 +0000
permalink:  creating_a_meal_prep_app_using_rails
---


For this project I wanted to create something that I could use and further build upon down the road. I recently got into actively monitoring my nutrition. I quickyl found that something that seems so simple as "counting calories" can quickly get out of hand an take a large portion of the day. My objective with this rails app is to find a way to create weekly menus that contain step by step recipes and also display macros for easy tracking. I want a user to log in, create recipes (or use recipes created by other users), build menus, and actively track their macros on a week by week basis. 

The first steps for me were to set up my environment and create my migrations. This project uses rails 5.2 and all the gems associated with that version. Gems I added to help with my authentication, testing, and secuity are shown below. 

```
gem 'bcrypt', '~> 3.1.7'
gem 'pry'
gem 'omniauth'
gem 'omniauth-facebook'
gem 'dotenv-rails'
```

After finding the gems I would need to add to help with this project the next step was to create the migrations, models, controllers, etc. that would go into building this app (Rails g model/controller) helped alot with this task. The Final schema for my app is shown below. 

```
ActiveRecord::Schema.define(version: 2018_08_27_164946) do

  create_table "meals", force: :cascade do |t|
    t.date "date", null: false
    t.integer "menu_id", null: false
    t.integer "recipe_id", null: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    t.index ["menu_id"], name: "index_meals_on_menu_id"
    t.index ["recipe_id"], name: "index_meals_on_recipe_id"
  end

  create_table "menus", force: :cascade do |t|
    t.date "start_date", null: false
    t.date "end_date", null: false
    t.integer "user_id", null: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    t.index ["user_id"], name: "index_menus_on_user_id"
  end

  create_table "profiles", force: :cascade do |t|
    t.integer "user_id"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "recipes", force: :cascade do |t|
    t.string "name", null: false
    t.text "description", null: false
    t.integer "calories", null: false
    t.integer "carbs", null: false
    t.integer "protein", null: false
    t.integer "fats", null: false
    t.integer "user_id", null: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    t.index ["user_id", "name"], name: "index_recipes_on_user_id_and_name"
    t.index ["user_id"], name: "index_recipes_on_user_id"
  end

  create_table "users", force: :cascade do |t|
    t.string "first_name"
    t.string "last_name"
    t.string "email"
    t.string "password_digest"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    t.string "uid"
  end

end
```

Developing the most of the user features (create, edit, login, logout, etc.) are very standard and can be found in lots of rails tutorials on youtube, medium, stack overflow, or a simple google search. Because of this I will not be going into details on developing the user component of the lab. For this lab I will be spending the remaining portion of this blog talking about developing the main feature, a user's weekly menu. To do this we need to take a step back and in simple english describe what a menu is. 

We've all been to restaurants, and have used menus to pick out a certain item to eat. To model this activity in a rails app one would believe that all you would need is a simple menu model, and a recipe model. The menu would hold all recipes while a recipe would contain the necessary steps to complete a dish. This is actually where I began, but quickly realized I would need something else in order to make this action work. Which led me to the question what are we accomplishing when we use a menu to pick a recipe? Well, we are creating a meal. We all know what a meal is, but how can we model that in a computer program. A meal is just a simple joins table. We are taking the recipe and putting it on a user's menu we are saying that a user is creating a "meal" for which they will eventually eat. To design this behavior I created the following associations. 

```
class Meal <ApplicationRecord
belongs_to :menu, 
belongs_to :recipe, 
end 

class Menu<ApplicationRecord
has_many :meals, -> { order(:date) }, inverse_of: :menu, dependent: :destroy
  has_many :recipes, through: :meals
  accepts_nested_attributes_for :meals
end 

```

These assocaitions would allow me the user to pick certain recipes  and put them on my menu, thus creating "meals" that I will eventually be able to consume. 
