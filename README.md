
# SmartyPy
Simple repository featuring duplicated Matlab/Octave and Python Machine Learning utilities. Made to pair with the Coursera Machine Learning introduction course taught by Andrew Ng.
https://www.coursera.org/specializations/machine-learning?utm_source=gg&utm_medium=sem&campaignid=655866957&adgroupid=34113811915&device=c&keyword=coursera%20machine%20learning&matchtype=p&network=g&devicemodel=&adpostion=1t1&creativeid=119391632947&hide_mobile_promo&gclid=CjwKEAjwmMS-BRCm5dn51JLbp1wSJACc61tF1ufsQo0cWmXtoBUISADwzRp9PQf8vZAxP1N3APWpTRoC7-vw_wcB

### Intent and Audience
This is predomenantly intended to be a learning exercise. Not at upstaging existing and sophisticated machine learning libraries

### Notation Convention
Summarized from: https://share.coursera.org/wiki/index.php/ML:Linear_Regression_with_Multiple_Variables

    n:      Number of features (excluding x0 feature)
    m:      Number of examples/samples
    x:      Feature column vector dataset (m x 1)
    X:      Feature or Design Matrix (m x n+1)
    Xn:     Normalized Feature Matrix (m x n+1)
    y:      Target/Solution vector (m x 1)
    J:      Cost of a sample (single value)
    theta:  Regression Coefficient Vector (n+1 x 1) ==> theta0 + theta1*x1 + theta2*x2 ... + thetan*xn
    h:      Hypothesis of form: h(X) = X @ theta
                                h(x) = theta.T @ x ==> [ --- theta --- ] @ [x]

    *Neural Networks*
    L:      Number of layers in a network
    sl:     Number of neurons/nodes in a layer. Not including bias node
    a:      Neural Network "Activation" layer.
    theta:  Weights matrix  mapping layer [j-1] to layer [j]
    z:      Intermediate variable for activation layer computation:
                z(j) = Theta(j-1) @ a(j-1)
                a(j) = sigmoid(zj)

Visual

            ┌                  ┐
            | 1   x1^1    xn^1 |
    X ==>   | 1   x1^2    xn^2 |
            |         ...      |
            | 1   x1^m    xn^m |
            └                  ┘


## Setup

### Python
Anaconda's Python distribution is recomended for getting started. Easiest way to get started is to install and create a new Python 3.5 environment from the included (not minimal) `py-environment.yml` file. 
http://conda.pydata.org/docs/using/envs.html. 

**Disclaimer 1: I don't test this all the time so this could be missing a dependency or two. Should get you 99% of the way there though**
**Disclaimer 2: Tensorflow isn't as simple as `conda install tensorflow` if you're using GPU stuff so you've probably gotta go figure out how to do that https://www.tensorflow.org/versions/r0.11/get_started/os_setup.html**

```
$ conda env create -f py-environment.yml
```
Otherwise, if you don't have Anaconda, a working Python 3.5+ environment (yes, you need 3.5+) and a few ancilary modules is all you need. 

### Clone And Run
Contact me for direct repo access, or Fork and create Pull Requests as needed. Any contributions and updates are welcome, this repo is definitely still in the early development stages. 
```
git clone git@github.com:ZachDischner/smartypy.git
```

### Examples
Check out example projects and demos within the `projects` directory. 

#### Simple Neural Networks
Check the `projects/smarty-NeuralNetworkDemo.ipynb` notebook for inteactive demonstration and usage case. 

Simple Example:
```
from smartypy import neuralNetwork
## Form a simple 2-layer SupervisedNeuralNetwork Object with loaded X sample array and y solutions
snn = neuralNetwork.SupervisedNeuralNetwork(X,y, 
                                            num_classifications=num_classifications, 
                                            regularization=1.0)
# Note, object determines input and output layer size automagically
print(snn)
snn.train(max_iter=10)
print("Trained Neural Network with final cost calculated as J = {}".format(snn.J))
```
![Notebook](http://i.imgur.com/fk2c6cz.png)

#### Simple K-Means Unsupervised Classification
See `projects/Smarty-Kmeans.ipynb` for demo of a basic K-Means clustering algorithm. 

**Illustrated Clustering Evolution**
![Illustration](http://i.imgur.com/ZYuOibW.png)

**Image Compression with K-Means Clustering**
![Imgur](http://i.imgur.com/JiL3I39.png)

#### Linear Regression
In smartypy top level directory: `ipython`
```
import pandas as pd
import numpy as np
import smartypy.linearRegression

## Read dataset
df = pd.read_csv("test/data/ex1data2.txt",names=["area","rooms","price"])

## Form X and y arrays
y=df.price.values
X=np.ones((len(y),3))
X[:,1]=df.area.values
X[:,2]=df.rooms.values

## Normalize X 
Xn,mu,sigma=smartypy.linearRegression.normalize_features(X)

## Gradient Descent
theta, Jhistory = smartypy.linearRegression.gradient_descent(Xn,y,[0,0,0],0.01)

## Somewhat helpful plotting utilities
```
![3D Linear Regression Plot](http://i.imgur.com/LrzZcv5.png)

#### Logistic Regression
* Run `logisticRegression.py` for a demonstration of logistic fitting
* From `logisticRegression._test_regularized()`:
```
import pandas as pd
import numpy as np
from smartypy import logisticRegression 

## Regularization and Polynomial Mapping Degree
lam = 1.0
poly_degree = 6
df = pd.read_csv("test/data/ex2data2.txt",names=["Microchip Test 1","Microchip Test 2","PassFail"])
X = np.array(df.iloc[:,0:2])
y = np.array(df["PassFail"])

## Prepend the theta0 column to X, form initial theta
X = np.insert(X, 0, 1, axis=1)
theta_init = np.zeros(X.shape[1])

## Plot
logisticRegression.plot_data(X,y,xlabel="Microchip Test 1",ylabel="Microchip Test 2", pos_legend="Pass",neg_legend="Fail")

## Map and setup problem
Xp = logisticRegression.polymap(X,degree=poly_degree)
theta_init = np.zeros(Xp.shape[1])

## Compute regularized cost and gradient
J = logisticRegression.compute_cost(Xp,y,theta_init,lam=lam)
grad = logisticRegression.compute_gradient(Xp,y,theta_init,lam=lam)

## Solution
# Attempts a minimization of the logistic regression cost function using 
#   a few methods from the scipy.optimize library
J, theta = logisticRegression.train_regression(X,y,poly_degree=poly_degree,lam=lam)
logisticRegression.plot_data(X,y,theta=theta,decision_boundary=True,poly_degree=poly_degree,xlabel="Microchip Test 1",ylabel="Microchip Test 2", pos_legend="Pass",neg_legend="Fail")

## Predict pass fail on new sample
passfail = logisticRegression.predict_sample(theta, [1,3,0],poly_degree=6)
# >> 0   This definitely falls outside the pass failure area

p = logisticRegression.predict_sample(theta,Xp)
training_accuracy = (p==y).mean()*100.0
```
![2D Decision Boundary Plot](http://i.imgur.com/CquuS0X.png)

* `BinaryClassifier` class can be used for training logistic regression classifier with polynomial prediction:
```
## X (100 x 2)and y (100 x 1) loaded from above sample
bp3 = logisticRegression.BinaryClassifier(X,y,poly_degree=3)
bp3.train()
bp3.classify(np.array([50,65]))
```

* Demonstration of One-vs-all logistic regression on dataset of numeric handwriting is included in `logisticRegression._test_multi()`. You'll need to get the dataset yourself to run this test. Contact maintainer or obtain from course materials.
![Numbers](http://i.imgur.com/t0xvK79.png)

## Todo
Never ending list. Top among them:

* Testing cases with `pytest` 
* Create Linear Regression class with polynomial mapping, etc stored in class for easier use
* Consolodate minimization functionality that is sorta duplicated in all modules into a single one, with kwargs to choose the minimization. Better yet, logic to choose the best minimization for the given problem (scipy.optimize has a good discussion therein)
* `numba.njit` optimizations

        
