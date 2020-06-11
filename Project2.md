Crazy Crazy-8s
========================================================
author: Ryan Kyaw, Makenna Drye, Mark Ramos
date: May 15, 2019
autosize: true
incremental: true

Welcome 
========================================================

- Crazy 8 comes from the iOS app, GamePigeon
- 63 games
- Main Goal: Can any variables tell you who is more likely to win a game of Crazy-8s?

Data Dictionary
========================================================

- Color Change: Change color and draw-4 change color cards
- Special: Draw-2, skip, and reverse cards
- Wild: Any cards that fall in the color change or special category

Data Cleaning
========================================================



- Created a "wild" card variable that combines the color change and special card variables 


```r
df$Wild.Beg.Mark <- df$Color.Change.Beg.Mark + df$Special.Beg.Mark
df$Wild.Beg.Makenna <- df$Color.Change.Beg.Makenna + df$Special.Beg.Makenna
df$Wild.Beg.Ryan <- df$Color.Change.Beg.Ryan + df$Special.Beg.Ryan
df$Wild.Drawn.Mark <- df$Change.Color.Drawn.Mark + df$Special.Drawn.Mark
df$Wild.Drawn.Makenna <- df$Change.Color.Drawn.Makenna + df$Special.Drawn.Makenna
df$Wild.Drawn.Ryan <- df$Change.Color.Drawn.Ryan + df$Special.Drawn.Ryan
```

Data Cleaning
========================================================

- Created a categorical variable that labels games as short (turns - [0,20]), medium (turns - [21, 40]), long (turns - [41, 60]), and very long (turns > 60).


```r
df$gamelength <- cut(df$Turns, c(min(df$Turns)-1, 20, 40, 60, max(df$Turns)+1), labels = c("short", "medium", "long", "verylong"))
```

- Developed a total cards drawn variable that adds up the cards drawn by each of the players


```r
df$total.cards.drawn <- df$Cards.Drawn.Makenna + df$Cards.Drawn.Mark + df$ Cards.Drawn.Ryan
```

Exploratory Data Analysis
========================================================

- How many games did each player win?

  - Ryan won 24 games
  - Makenna won 20 games
  - Mark won 19 games (Mark sucks)

Exploratory Data Analysis
========================================================
incremental: false 

- Game Length vs Cards Drawn 
- Clear positive correlation exists

<center>
![plot of chunk unnamed-chunk-5](Project2-figure/unnamed-chunk-5-1.png)

Exploratory Data Analysis
========================================================
incremental: false 

- Wild Cards Started With vs Game Winner
  
  - Ryan

<center>
![plot of chunk unnamed-chunk-6](Project2-figure/unnamed-chunk-6-1.png)

Exploratory Data Analysis
========================================================
title: false
incremental: false 

- Makenna

<center>
![plot of chunk unnamed-chunk-7](Project2-figure/unnamed-chunk-7-1.png)

Exploratory Data Analysis
========================================================
title: false
incremental: false 

- Mark

<center>
![plot of chunk unnamed-chunk-8](Project2-figure/unnamed-chunk-8-1.png)

Exploratory Data Analysis
========================================================
incremental: false

- Cards Drawn vs Game Winner

  - Ryan 
  
<center>
![plot of chunk unnamed-chunk-9](Project2-figure/unnamed-chunk-9-1.png)

Exploratory Data Analysis
========================================================
title: false
incremental: false 

- Makenna

<center>
![plot of chunk unnamed-chunk-10](Project2-figure/unnamed-chunk-10-1.png)

Exploratory Data Analysis
========================================================
title: false
incremental: false 

- Mark

<center>
![plot of chunk unnamed-chunk-11](Project2-figure/unnamed-chunk-11-1.png)

Questions 
========================================================

-Broad Questions

  - Do any variables impact the winner of a game of Crazy-8s?
  - How random is a game like Crazy-8s or UNO?
  
-Narrow Questions
  
  1. Can we confirm the positive correlation between game length and number of turns?
  2. Does the amount of wild cards held at the beginning of each game truly have no effect on the winner of the game?
  3. Can we confirm that the winner of each game drew less cards?
  4. Can we use some of the variables to create a model that effectively predicts the winner of each game?
  
Questions 
========================================================
title: false

Question 1. Can we confirm the positive correlation between game length and number of turns?

  - Linear Regression Model 
 
Questions 
========================================================
title: false

Question 2. Does the amount of wild cards held at the beginning of each game truly have no effect on the winner of the game?

  - ANOVA Test

Questions 
========================================================
title: false

Question 3. Can we confirm that the winner of each game drew less cards?
  
  -ANOVA Test

Questions 
========================================================
title: false

Question 4. Can we use some of the variables to create a model that effectively predicts the winner of each game?

  - Decision Trees
    - Our sample size is fairly small, so this could affect our model. 

Tests & Results 
========================================================

- Our Linear Regression model for question 1
- Null Hypothesis: The coefficient for each predictor variable is 0.


```r
lm.fit <- lm(Turns ~ total.cards.drawn, data = df)
summary(lm.fit)
```

Tests & Results
========================================================
title: false 
incremental: false 

![plot of chunk unnamed-chunk-13](lm.pres.png)
 
Tests & Results
========================================================
title: false

- Low p-value -> rejected Null hypothesis
- The coefficients do not equal 0
- We can confirm that there is a strong correlation between cards drawn and game length

  - R-squared = 0.9162
  
Tests & Results
========================================================

- Our ANOVA tests for question 2
- We have to run 3 tests for each player 
- Null Hypothesis: There is no difference in the average amount of wild cards that a player started with no matter the winner.


```r
summary(aov(Wild.Beg.Makenna ~ Winner, data = df))
```


```r
summary(aov(Wild.Beg.Mark ~ Winner, data = df))
```


```r
summary(aov(Wild.Beg.Ryan ~ Winner, data = df))
```

Tests & Results
========================================================
title: false
incremental: false

- Wild Cards that Makenna started with 


```
            Df Sum Sq Mean Sq F value Pr(>F)
Winner       2   1.16  0.5803   0.497  0.611
Residuals   60  70.11  1.1685               
```

Tests & Results
========================================================
title: false
incremental: false

- Wild Cards that Mark started with 


```
            Df Sum Sq Mean Sq F value Pr(>F)
Winner       2   3.72   1.861   1.497  0.232
Residuals   60  74.60   1.243               
```

Tests & Results
========================================================
title: false
incremental: false

- Wild Cards that Ryan started with 


```
            Df Sum Sq Mean Sq F value Pr(>F)
Winner       2   0.24  0.1199   0.071  0.931
Residuals   60 100.84  1.6807               
```

- All p-values are much greater than 0.05 and not statistically significant.
- We fail to reject the null
- There is no relationship between wild cards at the beginning of a game and winning that game

Tests & Results
========================================================

- Our ANOVA tests for question 3
- Once again, 3 tests for each player
- Null Hypothesis: The average amount of cards drawn for each player is the same no matter who wins the game. 


```r
summary(aov(Cards.Drawn.Makenna ~ Winner, data = df))
```


```r
summary(aov(Cards.Drawn.Mark ~ Winner, data = df))
```


```r
summary(aov(Cards.Drawn.Ryan ~ Winner, data = df))
```

Tests & Results
========================================================
title: false
incremental: false

- Cards drawn by Makenna


```
            Df Sum Sq Mean Sq F value Pr(>F)
Winner       2  124.1   62.04   1.623  0.206
Residuals   60 2294.1   38.24               
```

Tests & Results
========================================================
title: false
incremental: false

- Cards drawn by Mark


```
            Df Sum Sq Mean Sq F value Pr(>F)
Winner       2  137.6   68.80   1.394  0.256
Residuals   60 2962.3   49.37               
```

Tests & Results
========================================================
title: false
incremental: false

- Cards drawn by Ryan


```
            Df Sum Sq Mean Sq F value Pr(>F)
Winner       2   67.9   33.95    0.86  0.428
Residuals   60 2368.0   39.47               
```

- All p-values are greater than 0.05 and not statistically significant.
- We fail to reject the null
- Goes against our null hypothesis
  - Cannot confirm our graphs that appeared to show that the winners of games drew less cards
  
Tests & Results
========================================================

- Train/Test Framework
  - We split our data 50/50 because of the small size of our data set. 


```r
df <- df %>% mutate(id = row_number()) 
traindf <- df %>% sample_frac(0.5)
testdf <- df %>% anti_join(traindf,by='id')
write.csv(testdf,"test.csv",row.names = FALSE)
rm(testdf)
traindf$id <- NULL
```
  
Tests & Results
========================================================
title: false

- Cleaned data up to only include the necessary variables 


```r
newtraindf <- traindf %>% 
  select(Cards.Drawn.Mark, Cards.Drawn.Makenna, Cards.Drawn.Ryan, Wild.Beg.Mark, Wild.Beg.Makenna, Wild.Beg.Ryan, Winner)
```

Tests & Results
========================================================
title: false 

- Decision Tree Model 


```r
library(tree)
mymodel <- tree(Winner ~ ., data=newtraindf)
```

Tests & Results
========================================================
title: false
incremental: false


```r
plot(mymodel)
text(mymodel,pretty=0)
```

![plot of chunk unnamed-chunk-29](Project2-figure/unnamed-chunk-29-1.png)

Tests & Results
========================================================
title: false

- Confustion Matrix for the training data 


```r
newtraindf$pred <- predict(mymodel,type="class")
table(newtraindf$Winner,newtraindf$Winner)
```

```
         
          Makenna Mark Ryan
  Makenna      12    0    0
  Mark          0    8    0
  Ryan          0    0   12
```

Tests & Results
========================================================
title: false

- Bringing back the test data 






```r
newtestd4$pred <- predict(mymodel, newtestd4, type="class")
table(newtestd4$pred,newtestd4$Winner)
```

```
         
          Makenna Mark Ryan
  Makenna       7    3    4
  Mark          1    5    3
  Ryan          0    3    5
```

Tests & Results
========================================================

- Our decision tree does not generalize to the data very well
- Could be because of a small sample size
- Not a good model to predict game winner

Conclusion
========================================================

- Our data exploration and hypothesis testing prove that Crazy-8s is very random and luck-based. 
- None of the variables that we collected appeared significant in predicting the winner of a game

  - Even cards drawn couldn't be confirmed because of our ANOVA tests
  
-A larger sample size could have helped create more accurate models.
- A game like Crazy-8s does not involve the human element of shuffling, and this increases the level of randomness seen in this game. 

