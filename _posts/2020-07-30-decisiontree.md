---
layout: post
title: Implementing a Regression Decision Tree
image: /img/path.jpg
---
A decision tree is a predictive modeling approach that separates data into classes using a top-down appraoch, 
with broader classes at the top and more specific classes as you travel down the tree.

There are two types of problems you can use a decision tree to solve: classification and regression problems. 
Classification trees predict a discrete or categorical class label, such as dog or cat. Regression trees 
predict a continuious quanitiy or numeric output, such as $35.98 or 11.41 inches. This distinction is also 
important to note because the metrics used to construct (choose how the variables at each step split) 
classification and regression trees are different. 

<center><img src="https://miro.medium.com/max/1414/1*nMDP48LmXR0J9R434tSH0A.png" width="600" height="450"></center>

Examples used for classification trees include Gini impurity, Information gain (Entropy), and Chi-square. 
The [sklearn.tree.DecisionTreeClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeClassifier.html) uses gini or entropy as the criteria to measure the quality of a split. 
For regression trees, Variance reduction is used as the metric. The [sklearn.tree.DecisionTreeRegressor](https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeRegressor.html#sklearn.tree.DecisionTreeRegressor) use mean 
squared error (MSE), friedman mse, and mean absoulte error. For this project, I focus on implementing a regression 
decision tree.

Because of the overall structure of a decision tree, I created two classes: a Node class, and the DTRegressor 
class. The regressor class will house the fit and predict functions, and the node class will perform any 
splits. If the data is able to be split, it will always create two children.

The DTRegressor class relies on the numpy library and the Node class to fit data and make a prediction on new 
data. It takes in two parameters, X and y.  
```
def fit(self, X, y):
    self.tree = Node(X, y, np.array(range(len(y))))
    return self
```  
X represents an array of features where each row is an observation 
or sample and each column is a differnt feature. The y array are the true values observed for each observation. 
For example if we were trying to predict hosing prices, number of room and square feet would be features and the 
target or y would be the actual price of the home. This input might look like X = [[3, 1500]], y =[100000], where 
the home has 3 bedrooms, 1500 square feet and is valued at $100,000. The index of X corresponds with the same index of y. 

The Node class is a helper class that executes splitting the decision tree. For regression trees, some measure 
of variance is used as a metric to determine how the tree will branch off. In this case I used standard 
deviation. The find_split method finds the best split in each columns (or for each feature).  
```
def find_split(self):
    # find the best feature to split on
    for feature in range(self.features): 
        self.find_best_split(feature)
``` 
And the 
find_best_split finds at which row for each feature does the best split occur.  
```
def find_best_split(self, feature_index):
    # select feature column
    x = self.X.values[self.index, feature_index]

    # for each observation in row
    for sample in range(self.samples):
        # separate the data by each observation
        left = x <= x[sample]
        right = x > x[sample]
        # find the weighted averge score 
        score = self.get_score(left, right)
        # store lowest score in self.score
        if score < self.score:
            self.feature_index = feature_index
            self.score = score
            # store feature and observation
            # where score is minimized
            self.split = x[sample]
```  
The best fit will have the 
smallest variance. Therefore, the split that minimizes the standard deviation of the node is picked. If the 
lowest std is still higher than the previous node, the node does not split. This means we are at the terminal 
node of the tree. 

If we want to make a prediction, the predict method starts at the root node and work is way
down to the terminal node and return the predicted y value.  
```
def predict_row(self, xi):
    # start with root node
    if self.is_terminal: 
        return self.value
    # decide if we need to travel left or right
    # continue until reach terminal node
    # return the value
    node = self.left if xi[self.feature_index] <= self.split else self.right
    return node.predict_row(xi)
``` 

For more information behind the math of regression trees see this [video](https://www.youtube.com/watch?v=g9c66TUylZ4) 

**SKLEARN Comparison**  
To test this model, lets first import some data. The Boston Housing dataset has 13 features, but 
lets just use two. ```target:"MVP"``` ```features: "rooms", "age"```  
```
# Imports 
import numpy as np
import pandas as pd
from sklearn.datasets import load_boston
from sklearn.tree import DecisionTreeRegressor

# The Boston Housing Dataset
X, y = load_boston(return_X_y=True)
columns = ["crime","zone","industry","river","nox_con","rooms","age",
           "distance","highways", "tax", "school","B","lstat"]
house = pd.DataFrame(data=X, columns=columns)

# Features and target
target = y
features = house[["rooms", "age"]]

# Fit Tree
tree = DecisionTreeRegressor()
tree.fit(features,target)

# Predict
tree.predict([[6,65], [2,10], [3,25], [7,100]])
```  
```>>> array([22.9, 16.1, 16.1, 36. ])```

Now for my regression class...
```
from node import Node 
from DTRegressor import DTRegressor
# Fit Tree
m_tree = DecisionTreeRegressor()
m_tree.fit(features,target)

# Predict
m_tree.predict([[6,65], [2,10], [3,25], [7,100]])
```  
```>>> array([22.9, 16.1, 16.1, 36. ])```

The result of my decision tree seems to work really well. I was worried my results would vary greatly becaused I used 
standard deviation as my metric. Many difffernt sources for regression trees use differnt metrics for their criteria for
splitting the tree. Sklearn uses mean squared error, while other common metrics I came across were standard deviation,
reduction in variance, root mean square error, and sum of the squared residuals. I first desired to follow along with the
sklearn model to see how accurate my tree was, but I could not figure out how they were calculating the mse for each split.
Therefore, I went with another option of splitting my tree based on standard deviation.
