---
layout: post
title:      "Submitting a rails form with AJAX"
date:       2019-03-11 17:37:18 +0000
permalink:  submitting_a_rails_form_with_ajax
---


One of the more challenging parts of my Javascript portfolio project was adding in the functionality to submit a new recipe with ajax. I kept getting an error that no matter how many visits to stack overflow or google searches I just couldnt solve. 

The error was a simple 404 url not found. What I was doing was pulling recipe data from what appeared (at least from a front end prespective) to be a form. I would take each input and save it and the hopefully pass this data on to localhost:3000/users/id/recipes.json. When it came time to "submit" my form I would get this 404 error stating that my url didnt exist, even though through a simple copy and paste it was present. What was the issue? Lets take a look at the code.

```
<h1>Create a New Recipe</h1>	
    <div class="recipe-form-group">	
      Recipe Name:<br>	
      <input type="text" class="recipe-name-form" placeholder="Name"><br>	
    </div>	
    <div class="recipe-form-group">	
      Recipe Description:<br>	
      <input type="text" class="recipe-description-input" placeholder="description"><br>	
    </div>	
    <div class="recipe-form-group">	
      Recipe Calories:<br>	
      <input type="number" class="recipe-calories-input" placeholder="calories"><br>	
    </div>	
    <div class="recipe-form-group">	
      Recipe Carbs:<br>	
      <input type="number" class="recipe-carbs-input" placeholder="carbs"><br>	
    </div>	
    <div class="recipe-form-group">	
      Recipe Protein:<br>	
      <input type="number" class="recipe-protein-input" placeholder="protein"><br>	
    </div>	
    <div class="recipe-form-group">	
      Recipe Fats:<br>	
      <input type="number" class="recipe-fats-input" placeholder="fats"><br>	
    </div>	
    <br>	
      <button class="create-recipe-submission-button" data-button="Hello">Submit Recipe</button>
```

```
function submitNewRecipe(){	
  let recipeName = $('.recipe-name-form').val();	
  let recipeDescription = $('.recipe-description-input').val();	
  let recipeCalories = $('.recipe-calories-input').val();	
  let recipeCarbs = $('.recipe-carbs-input').val();	
  let recipeProtein = $('.recipe-protein-input').val();	
  let recipeFats = $('.recipe-fats-input').val();	

   let recipeData = {name: recipeName, description: recipeDescription, calories: recipeCalories, carbs: recipeCarbs, protein: recipeProtein, fats: recipeFats};	

   let url = `recipes.json`;	

   $.ajax({	
    url: url,	
    type: "POST",	
    dataType: 'json',	
    data: recipeData	
  })	
  .done(function(json){	
    console.log("recipe updated");	
  })
```

Right of the back there is a big issue. How am I connecting my data to the recipe model? All I am trying to do in this above code is pull text from text boxes and post it to the recipes.json url. There is no actual connection to the Recipe model. This will bring up a myriad of issues with validation and bad data, but that is besides the point. So how can I fix this or connect to recipe model? The first step is an actual form build out using a conventional form_for. 

```
<div class="recipe-form">
<%= form_for @recipe do |form| %>

<%= form.hidden_field :user_id, value: @user.id %>

  <div class="recipe-form-group">
    <%= form.text_field :name, :placeholder => "Name" %>
  </div>

  <div class="recipe-form-group">
    <%= form.text_area :description, :placeholder => "Description" %>
  </div>

  <div class="recipe-form-group">
    <%= form.number_field :calories, :placeholder => "Calories" %>
  </div>

  <div class="recipe-form-group">
    <%= form.number_field :carbs, :placeholder => "Carbs" %>
  </div>

  <div class="recipe-form-group">
    <%= form.number_field :protein, :placeholder => "Protein" %>
  </div>

  <div class="recipe-form-group">
    <%= form.number_field :fats, :placeholder => "Fats" %>
  </div>
  <div class="recipe-submit-button">
    <%= form.submit%>
    </div>
<% end %>
</div>
```

Now I need to take this data and POST, and display data. 

```
unction attachRecipeCreateListener(){
  $(function () {
  $('form').submit(function(event) {
    //prevent form from submitting the default way
    event.preventDefault();
    let values = $(this).serialize();
    let posting = $.post('/recipes', values);
    posting.done(function(data) {
      let recipe = data;
      $(".new-recipe-container").remove();
      $(".profile-content").append(`
        <div class="recipe-content">
          <h2>Name</h2>
          <p>${recipe.name}</p>
        </div>
        <div class="recipe-content">
          <h2>Description</h2>
          <p>${recipe.description}</p>
        </div>
        <div class="recipe-content">
          <h2>Calories</h2>
          <p>${recipe.calories}</p>
        </div>
        <div class="recipe-content">
          <h2>Carbs</h2>
          <p>${recipe.carbs}</p>
        </div>
        <div class="recipe-content">
          <h2>Protein</h2>
          <p>${recipe.protein}</p>
        </div>
        <div class="recipe-content">
          <h2>Fats</h2>
          <p>${recipe.fats}</p>
        </div>  `);
     });
  });
});
}
```

What the above code is doing is deleting the recipe form while displaying the newly create JSON recipe object. In test everything works as it should, and I learned a valuable lesson about the front end and the backend. Even if your html looks like a form you need to make sure that it has the right form functionality. 


