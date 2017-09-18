# Model Evaluation & Validation
*Notes on Lesson #4 of Udacity's Deep Learning Course*

## Regression & Classification
- Remember, **regression** involves finding an approximation for a numeric value
- **Classification** attempts to estimate state

## Testing
- When evaluating how well training has gone, it's important to train with valid data
- Although a model might be able to perfectly predict values based off its training data, it might be terrible in testing & validation
- When a model works *too well* on the training data, but relatively poorly on the testing data, the model is said to be **overfitted**
- **overfitting** is when the model makes predictions nearly exactly based off training data, and doesn't consider well enough the potential hidden relations and noise of the training data and will fail evaluating the test data
- `sklearn`, as used in linear regression helps a lot in neural networks as well, when testing and evaluating models, use `train_test_split` like this:
```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y)
```
- **Thou shalt never use testing data for training**
  - This nearly guarantees that **overfitting** will occur
  - Easier to break this rule than most initially assume
- When evaluating a model, four possibilities happen when classifying it:
  - True positives are when the model predicts correctly, an affirmative
  - True negatives when predictions of the non-affirmative are correct
  - False positives will probably have been heard of before, it's when a prediction in the affirmative is made incorrectly
  - False negatives, when non-affirmative predictions are incorrect.
- It is useful to organize these results when evaluating a model into a **confusion matrix**, to compare how many of each result occurs
### Accuracy
- Out of all data points, how many were correctly classified?
- Accuracy becomes the number of correctly classified points $x_c$ of the total of data points $x_n$
$$ Accuracy = \frac{x_c}{x_n} = \frac{true_positive + true_negative}{true_+ + true_- + false_+ + false_- } $$
- In `sklearn` this is easily done with the `accuracy_score` function:
```python
from sklearn.metric import accuracy_score
accuracy_score(y_true, y_pred)
```
- There are some other ways to evaluate how good a model is

### Mean Absolute Error (MAE)
- The average distance of predictions from actual data
- This is done easily through, again, `sklearn`
- This can be problematic however if using MAE for gradient descent, as there is no differential to MAE
```python
from sklearn.metrics import mean_absolute_error
from sklearn.linear_model import LinearRegression

classifier = LinearRegression()
classifier.fit(X,y)

guesses = classifier.predict(x)

error = mean_absolute_error(y, guesses)
```

### Mean Squared Error
- MSE has differentials
- Adds squares of distances of points of data and line of model
- Again, `sklearn` already has this stuff baked in
```python
from sklearn.metrics import mean_squared_error
from sklearn.linear_model import LinearRegression

classifier = LinearRegression()
classifier.fit(X, y)

guesses = classifier.predict(X)

error = mean_squared_error(y, guesses)
```

### R2 Score
- Takes the simplest possible model and compares to it
- One of the simplest models is to simply average the data and draw a horizontal line representing the average
- Then it's possible to do R2 scoritng by taking the MSE of average line
- It would hopefully result in a **much** larger error than the regular MSE of the model in question
- The R2 score becomes:
$$ R2_{score} = 1 - \frac{MSE_{actual}{MSE_{simple}}} $$
- Bad models would then have similar errors between the simple and actual models
  - The R2 score close to 0 is bad
- Good models will have the MSE for the model be much smaller than the simple average model
  - Therefore Good models will have an R2 score as close to 1 as possible
- An R2 score close to 0 means that random guesses are almost as good as the model
- Again, `sklearn` has another metrics function to get an `r2_score`
```python
from sklearn.metrics import r2_score

y_true = [1, 2, 4]
y_pred = [1.3, 2.5, 3.7]

r2_score(y_true, y_pred)
```

