---
layout: post
title: Implementing a Regression Decision Tree
image: /img/path.jpg
---
A decision tree is a predictive modeling approach that separates data into classes using a top down appraoch, 
with broader classes at the top and more specific classes as you travel down the tree.  
There are two types of problems you can use a decision tree to solve: classification and regression problems. 
Classification trees predict a discrete or categorical class label, such as dog or cat. Regression trees 
predict a continuious quanitiy or numeric output, such as $35.98 or 11.41 inces. This distinction is also 
important to note that the metrics used to contruct (choosing how the variavles at each step split) 
classification and regression trees are different. 

<center><img src="`r https://miro.medium.com/max/1414/1*nMDP48LmXR0J9R434tSH0A.png`"></center>

Examples used for classification trees include Gini impurity, Information gain (Entropy), and Chi-square. 
The sklearn.tree.DecisionTreeClassifier uses gini or entropy as the criteria to measure the quality of a split. 
For regression trees, Variance reduction is used as the metric. The sklearn.tree.DecisionTreeRegressor use mean 
squared error (MSE), friedman mse, and mean absoulte error. For this project I focus on implementing a regression 
decision tree.

Because of the overall structure of a decision tree, I created two classes: a Node class, and the DTRegressor 
class. The regressor class will house the fit and predict functions, and the node class will perform any 
splits. If the data is able to be split, it will always create two children.

The DTRegressor class relies the numpy library and the Node class to fit data and make a prediction on new 
data. It takes in two parameters, X and y. X represents an array of features where each row is an observation 
or sample and each column is a differnt feature. The y array are the true values observed for each observation. 
For example if we were trying to predict hosing prices, number of room and square feet would be features and the 
target or y would be the actual price of the home. This input might look like X = [[3, 1500]], y =[100000], where 
the home has 3 bedrooms, 1500 square feet and is valued at $100,000. The index of X corresponds with the same index of y. 

The Node class is a helper class that executes splitting the decision tree. For regression trees, some measure 
of variance is used as a metric to determine how the tree will branch off. In this case I used standard 
deviation. The find_split method finds the best split in each columns (or for each feature). And the 
find_best_split finds at which row for each feature does the best split occur. The best fit will have the 
smallest variance. Therefore, the split that minimizes the standard deviation of the node is picked. If the 
lowest std is still higher than the previous node, the node does not split. This means we are at the terminal 
node of the tree. If we want to make a prediction, the predict method starts at the root node and work is way
down to the terminal node and return the predicted y value.

<center><img src="https://miro.medium.com/max/1414/1*nMDP48LmXR0J9R434tSH0A.png"></center>
