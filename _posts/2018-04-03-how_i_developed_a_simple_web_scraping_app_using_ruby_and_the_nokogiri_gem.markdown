---
layout: post
title:      "How I Developed A Simple Web Scraping App Using Ruby and the Nokogiri Gem"
date:       2018-04-03 13:26:57 +0000
permalink:  how_i_developed_a_simple_web_scraping_app_using_ruby_and_the_nokogiri_gem
---


For my first major project I wanted to be able to lookup any hockey player’s stats from my console. I thought that it would be a great way to test out my skills with the nokogiri gem as well actually creating a full fledge CLI app with multiple files communicating with one another. I wanted this app (with a little extra work) to eventually be a useful ruby gem. So in building this app I used a ruby gem structure. 
The main structure of my app would be as followed:
1. bin
2. config
3. lib

The bin would run the CLI portion of my app while my config would connect all the files together. The lib would be where the main functionality of the app would reside. In the lib file I would need to direct the user through the CLI app, scrape the necessary data for the app's content, and parse the data in a way to display to the user. In this blog post I will be explaining how I was able to scrape and access the data from an external source using nokogiri. 



**Getting the Data**
The site I used to access the data is a site called https://www.hockey-reference.com/leagues/NHL_2018_skaters.html. Within this site there is a massive table containing all of the NHL Stats for every single hockey player.  What I wanted to do was pull this information and place it into an array. That I can eventually use for my user to access data. Using Nokogiri I initially needed to access the site's HTML to ge the data. This is accomplished using the line:
```
page = Nokogiri::HTML(open("https://www.hockey-reference.com/leagues/NHL_2018_skaters.html"))
```

Within the HTML I needed to access the large table as well as the individual contents of that table. There are alot of stats in this table so this block of code became quite large. 

```
page_table = page.css('.stats_table')
    page_table.css("table tr").each do |player|
      hockey_player = player.css(".left").css("[data-stat = 'player']").text
      player_age = player.css(".center").css("[data-stat = 'age']").text
      player_position = player.css(".center").css("[data-stat = 'pos']").text
      player_team = player.css(".left").css("[data-stat = 'team_id']").text
      player_games_played = player.css(".right").css("[data-stat = 'games_played']").text
      player_goals = player.css(".right").css("[data-stat = 'goals']").text
      player_assists = player.css(".right").css("[data-stat = 'assists']").text
      player_points = player.css(".right").css("[data-stat = 'points']").text
      player_plus_minus = player.css(".right").css("[data-stat = 'plus_minus']").text
      player_penalty_minutes = player.css(".right").css("[data-stat = 'pen_min']").text
      player_shooting_percentage = player.css(".right").css("[data-stat = 'shot_pct']").text
      player_toi_avg = player.css(".right").css("[data-stat = 'time_on_ice_avg']").text
      player_blocks = player.css(".right").css("[data-stat = 'blocks']").text
      player_hits = player.css(".right").css("[data-stat = 'hits']").text
      player_faceoff_percentage = player.css(".right").css("[data-stat = 'faceoff_percentage']").text
```

After pulling the individual contents of the table I needed to store this data so that it can enventually be accessed by the user. This is done using the code below: 

```
 @stats << {player: hockey_player, age: player_age, position: player_position, team: player_team, games_played: player_games_played, goals: player_goals, assists: player_assists, points: player_points, shooting_percentage: player_shooting_percentage, plus_minus: player_plus_minus, penalty_minutes: player_penalty_minutes, TOI_Avg: player_toi_avg, blocks: player_blocks, hits: player_hits, faceoff_percentage: player_faceoff_percentage}


```

With all this data now stored in my stats variable I could easily work with it using simple hash searches.

**Using the Data**

I knew for my CLI app my user would first select a team. After selecting a team the console would display the players for which the user would select from to get the stats. The user would then type in a player and the stats would appear. This task would require three core methods. A method that initially will run the scraper to get the info an team players, a method that will ask for a specific player, and finally a method that will get that player's stats.  These methods are shown below: 

```
  def team_players
    get_page  #Runs the scraper
    @stats.each do |team|
      if team[:team] == @team
        puts "#{team[:player]}"
        player = team[:player]
        @team_players << player
      end
    end
    puts "____________________________________________________________________"
  end
	
	  def player_pick
    puts "Which Player would you like to get stats for?"
    @input_player = gets.strip
    @team_players.include?(@input_player) ? player_stats_info : player_error
  end
	
	def player_stats_info
    @stats.each do |team|
      if team[:player] == @input_player
        puts "____________________________________________________________________"
        puts "#{team[:player]} Age:#{team[:age]} Position:#{team[:position]}"
        puts "Team:#{team[:team]}" 
        puts "Games Played: #{team[:games_played]}"
        puts "Goals: #{team[:goals]}"
        puts "Assists: #{team[:assists]}"
        puts "Points: #{team[:points]}"
        puts "Shooting Percentage: #{team[:shooting_percentage]}"
        puts "Plus Minus: #{team[:plus_minus]}"
        puts "Penalty Minutes: #{team[:penalty_minutes]}"
        puts "Average TOI: #{team[:TOI_Avg]}"
        puts "Blocks: #{team[:blocks]}"
        puts "Hits: #{team[:hits]}"
        puts "Faceoff Percentage: #{team[:faceoff_percentage]}"
        puts "____________________________________________________________________"
      end
    end
    another_player?
  end
```

Overall the bulk of my time for this project was figuring out how to scrape the data and once the data was scraped figuring out how to display said data. I feel this project gave me a better understanding of nokogiri as well as how to go from a blank page to a functioning CLI app using ruby. 






