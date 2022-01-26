# Capstone-Repo

## Introduction
This README details an overview of a project used to round off a General Assembly Data Science Bootcamp giving insight to the workings of the project to an outsider.

## Contents

## Project Goals
The project goal was to produce a classifier model that could predict the outcome of a football match from its first half statistics reliably with an accuraccy higher than the baseline. Potential outcomes were:  
  
  • Home team win  
  • Away team win  
  • Draw  
 
 Further analysis was carried out on feature importances to investigate which contributed most to the match outcome.
 
 ## Data
 The data was obtained from Statsbomb, a football analytics company that investigate football data to provide insights that can benifit teams' performance, provide metrics for  quality media analysis as well as create strong predictive models for gambling companies. Statsbomb has an open repository on GitHub containing data of 2000 matches from a variety of leagues and competitions over multiple years including international and national competitions for both male and female teams. The data is stored as JSON files and is split into different files that are linked by unique match and event IDs.  
  
The features are incredibly deatiled as the dataset is separated into 4 different file categoreies:
  
  • Events: Contains information of every event that occurred in a game for both teams. Each event has its own ID and file shows which team was in possession for each event and a host of other details like the event type, players invovled, the play style at the time of the event, event duration etc. Each event type also has its own set of possible attributes also detailed.   
    
  • Line-Ups: Contains information of the player line-ups for each team in the match.  
    
  • Matches: Multiple leagues are split individually into a folder which containts multiple seasons worth of data for the corresponding leagues. Each match in these folders has its own relevant events file. Match files provide general information for the game rather than the specifics and details that are found in the events files.  
    
• 360:  
  
The final goal with the data was to have one table that contained some aggregated team statistics for each match that could be used for modelling. Essentially, the event files needed to be compressed to a single row so that that the final table contained summarised statistics for each team in each match.
