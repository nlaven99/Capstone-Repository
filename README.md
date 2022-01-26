# Capstone Repository

## Introduction
This README details an overview of a project used to round off a General Assembly Data Science Bootcamp giving insight to the workings of the project to an outsider.

## Repository Contents
  
  • Notebook  
  • READMe  
  • Data (clean data for modelling, original data can be found at https://github.com/statsbomb/open-data)  

## Libraries

  • Pandas  
  • NumPy  
  • Glob  
  • Json  
  • Seaborn  
  • Matplotlib  
  • Sklearn  
  • Scikitplot  
  • SciPy  
  • tqdm  

## Project Goals
The project goal was to produce a classifier model that could predict the outcome of a football match from its first half statistics reliably with an accuracy higher than the baseline.

Outcome | Label 
------- | -----
Home Team Win | 0
Away Team Win | 1
Draw | 2

Further analysis was carried out on feature importances to investigate which contributed most to the match outcome.

## Data

The data was obtained from Statsbomb, a football analytics company that investigates football data to provide insights that can benefit teams' performance, provide metrics for  quality media analysis as well as create strong predictive models for gambling companies. Statsbomb has an open repository on GitHub containing data of 2000 matches from a variety of leagues and competitions over multiple years including international and national competitions for both male and female teams. The data is stored as JSON files and is split into different files that are linked by unique match and event IDs.  
  
The features are incredibly detailed as the dataset is separated into 4 different file categories:

• Events: Contains information of every event that occurred in a game for both teams. Each event has its own ID and file shows which team was in possession for each event and a host of other details like the event type, players involved, the play style at the time of the event, event duration etc. Each event type also has its own set of possible attributes also detailed.  

• Matches: Multiple leagues are split individually into a folder which contains multiple seasons worth of data for the corresponding leagues. Each match in these folders has its own relevant events file. Match files provide general information for the game rather than the specifics and details that are found in the events files.  

• Line-up and 360 files were also included in the original data with the former containing the player information and the later storing data for snapshots of the whole pitch during a given event (player positions and whether a player was directly involved in the represented event). With more time, these files could be incorporated into the project but the information was much more in depth than necessary for the core project.  

The final goal with the data was to have one table that contained some aggregated team statistics for each match that could be used for modelling. Essentially, the event files needed to be compressed to a single row so that the final table contained summarised statistics for each team in each match.

## Data Cleaning and Feature Engineering

The data cleaning process entailed pulling the necessary information out of the dictionaries in the event and match JSON files without the extra characters and unnecessary information that came with the JSON format.

The match files were cleaned first as they were closer to the desired structure for the final dataframe and it made more sense to add the event information afterwards. The files were read into pandas’ dataframes but due to the JSON format, only the first layer of dictionary key-value pairs were flattened during the pandas reading process. This meant that some cell values were still dictionaries. In some cases, nested dictionaries also existed. Due to the format of JSON files, required information can be pulled by indexing correctly. Despite existing in the dataframe cell, the corresponding dictionary can still be indexed into. 

Lists of the relevant details were created and all the match files looped through with needed details being appended to the previously empty lists. This allowed for the match order to stay consistent when adding the details, from list to list. Some data was missing for certain features. For example, not all home team managers had noted nationalities; in these cases, a null value was added instead. These lists then made up the initial few columns for the final dataframe.

The event data is slightly more complex in structure, all files are different shapes as different numbers of events happen each game. Not all event types happen in every game too. In order to fit the same structure as the unfinished dataframe from the match file information, the statistics for the first half were summed for each team, compressing the necessary information into a single row. 

Two functions were created that take an individual file as an input. One is for home team events and the other for away teams. As the event files don’t have an attribute specifying the difference between the two, only the name of the team that was in possession for the event, the first step was appending all the event type names and their possession teams to an empty list for each. Next, these lists were used to make a temporary data frame which then had the events by the wrong team filtered out with a boolean mask. After some investigating of the data, I noticed the first event was always a home team line-up. For the home function the mask was equal to the possession team name that appeared first and the opposite for the away team function. These filtered data frames had their value count data converted to a dictionary using in-built pandas methods. This left a single column rather than a row, so the remaining column was transposed and had its column names edited to have a home or away team prefix added.

![Home Function](https://github.com/nlaven99/Capstone-Repo/blob/main/Pictures/Home%20Function%20Code.png)

Parsing through a list of files created a blocker where the summed rows would not join together simply as each one had a different shape due to certain event types not occurring in every match. A large list was created containing the dataframes for every event file as each item. The home and away functions were applied on each item and the output was appended to a separate list for each team. To work around the shape irregularities, many empty lists were created for all possible events and a series of try/except statements were used to append the number of times that event happened or NaNs if that event hadn’t occurred. This was done separately for home and away teams and the lists for both were transformed into two more data frames. With all the pieces in the correct format a left join was performed on the home and away dataframes and this result was concatenated to get a final table ready to be used for the modelling.

Tqdm was also used to check progress of passing the full list of files through the prior methods and the features below were counted for home and away teams separately.

Modelling Features | Description
------------------ | -----------
Passes | Intended kick from a player to their team mate
Pressures | Field player applies pressure to opposition player in possession of ball via multiple methods
Shots | Attempts to score goals with any legal body part
Substitutions | Player taken off and replace due to multiple reasons
Tactical Shifts | Indicates a change in formation
Ball Recoveries | Attempts to recover loose balls
Bad Behaviour | Player receives a card due to events outside of play
Ball Receipts | Intended receipt of a pass
Blocks | Standing in front of the ball to prevent passes
Carries | Player holds the ball at their feet whilst standing still or moving
Clearances | Defending player clears danger without intention to pass to a teammate.
Dispossessions | Player is tackled by a defender and loses the ball without attempting a dribble
Dribbles | Attempt by player to beat opponent
Dribbles Past | Player is dribbled past by opponent
Own Goals Against | Own goal scored against the team
Errors | Player makes a mistake on-ball leading to an attempt on goal
Fouls Committed | Infringements penalised as fouls by the referee (Not offsides)
Fouls Won | Player wins a free kick or penalty after being fouled by opponent
Goalkeeper Events | Variety of actions done by goalkeeper
Interceptions | Preventing opponents pass from completion by reacting to incept
Miscontrols | Player loses ball via bad touch
Offsides | Non-pass offside outcomes (clearance or shots)
Duels | 50-50 contest between two players on opposing teams challenging to recover a loose ball
Own Goals For | Own goals scored for the team
Shields | Player shields ball to prevent opponent from keeping it in play


## EDA

The distributions of the passing and shot counts show a positive skew to the data. While the pass counts are almost identical for both teams, the shots show the home team to have slightly more on average. The similarity overall between the team distributions also suggests a level of consistency to team skill levels.

![Home Passes Distribution](https://github.com/nlaven99/Capstone-Repo/blob/main/Pictures/Home%20Pass%20Distribution.png) ![Away Passes Distribution](https://github.com/nlaven99/Capstone-Repo/blob/main/Pictures/Away%20Pass%20Distribution.png)

![Home Team Shots Distribution](https://github.com/nlaven99/Capstone-Repo/blob/main/Pictures/Home%20Shot%20Distribution.png) ![Away Team Shots Distribution](https://github.com/nlaven99/Capstone-Repo/blob/main/Pictures/Away%20Shot%20Distribution.png)

The relative baseline accuracies for each class can be seen in the pie chart below. It would be useful having more draw data for the classification model.

![Pie Chart](https://github.com/nlaven99/Capstone-Repo/blob/main/Pictures/Pie%20Chart.png)

## Modelling

Potential match outcomes were turned into binary-type labels. The final scores were included in the match data so a new column was added to the final data frame using a list that had the outcome labels contained if the relevant conditions were met. A subset of the necessary features was pulled and standardised using sklearn’s standard scaler with outcome as the target. The data was also separated into a train-test split. As the dataset was not as large as expected in the number of matches, none were left out of the modelling. Sklearn’s gridsearch was implemented on two models to optimise the hyperparameters thus obtaining the best score.

### Results and Findings

The model with the highest cross validated test score was the Gridsearched Logistic Regression model with a score of 0.699. The confusion matrix for this model shows that there were a high number of true positives compared to false positives for the 0 and 1 labels. However no draw (2) predictions were made.

![Model Confusion Matrix](https://github.com/nlaven99/Capstone-Repo/blob/main/Pictures/Confusion%20Matrix.png)

The classification report shows a reasonably high precision score for label 0 and slightly less so for label 1. As the precision score indicates the models ability to avoid incorrectly predicting a label as another, we see that the majority of labels 0 and 1 are true positives and this is supported by high recall scores, as the model identifies about 85% of the observations in the correct class for either team winning. However, no observations were predicted to be draws leaving a 0 accuracy for this label.

![Model Classification Report](https://github.com/nlaven99/Capstone-Repo/blob/main/Pictures/Classification%20Report.png)

We can see that the model succeeds on the accuracies for a teams winning but is significantly bad at predicting draws. This is further supported by the Precision-Recall curve and the Receiving Operator Characteristic (ROC) curve.

![Precision-Recall Curve](https://github.com/nlaven99/Capstone-Repo/blob/main/Pictures/Precision-Recall.png) ![ROC Curve](https://github.com/nlaven99/Capstone-Repo/blob/main/Pictures/ROC%20Curve.png)

Feature importances were found by using the Gridsearch’s best model estimator attribute followed by the built in feature importance attribute. These were turned into a data frame and the features with their importances were then displayed as a graph sort by increasing importance.

The most important features appear to be possession oriented followed closely by shots and less so by goalkeeper events, with home team events having a higher importance regardless.

![Top Feature Importances](https://github.com/nlaven99/Capstone-Repo/blob/main/Pictures/Top%20Feature%20Importance.png)

The least important features are rare events that tend to happen only once or twice if at all in a game. Some, like tactical shifts, are expected as this event in particular is likely to be a response to losing or suffering a blow to winning chances.

![Bottom Feature Importances](https://github.com/nlaven99/Capstone-Repo/blob/main/Pictures/Bottom%20Feature%20Importance.png)

## Conculusion

The model with the best score proved to be the Logistic Regression model. Upon further analysis, the confusion matrix and classification report show that the model can reliably predict whether the home team won, less so for predicting away team wins and cannot predict draws at all. The feature importance breakdown shows that possession statistics are the biggest factors leading to the outcome, closely followed by shots and then goalkeeper events. The home team possession features are more important than the away team features which demonstrates the home team advantage too. The lowest importance features are events that likely only happen a handful of times throughout the data. Offsides being among these is a little surprising as they are still more common but are suggested to have little impact on the outcome regardless.

## Limitations

The main limitation with this project was time, as the overall structure and immense feature detail took longer than anticipated to process. Certain features can be refined even further with goalkeeper events being a key one. More data would be useful too, especially draws, but Statsbomb needs to be contacted directly for this.

The variety in matches may have also affected the inferations as different leagues are not representative of the same styles of play and there will certainly be a discrepancy with the average team statistics of male and female teams. Having more matches of a certain league than others could influence the model's analysis.

## Further Work

Features can be refined further to give a more detailed view especially goalkeeper events. Only match and event files were used but the 360 and line-up files contain information that can provide some different insights. Finally running gridsearch on the unoptimised models would be useful too.

## Key Learnings

This project introduced me to data stored in JSON format, and by the end I became confident navigating the information structure. It also consolidated what I had learned in managing data with pandas due to the lengthy cleaning process and honed my data visualisation skills, from heatmaps during the EDA to comparing feature importance. My knowledge on classifier models was also challenged and doing model analysis has made me more comfortable when using them. 
