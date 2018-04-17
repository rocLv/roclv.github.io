---
  layout: post
  title: Logistic Regression with a Neural Network mindset
---

### Regression with a Neural Network mindset

This is Andrew Ng's Deeplearnig Course assignment 2-2
<!--more-->

#### Get `h5 file's information`

```python

import h5py

f = h5py.File('filename.h5', 'r')
print(str(list(f.keys())))

=> ['list_classes', 'test_set_x']
```

#### Reshape the training data

```python
X_flatten = X.reshape(X.shape[0], -1).T
```

PS: A trick when you want to flatten a matrix X of shape (a, b, c, d) to a
matrix X_flatten of shape (b*c*d, a) is to use:

```python
X_flatten = X.reshape(X.shape[0], -1).T


x = np.array([[1, 2, 3],
              [4, 5, 6]])
print("x's shape is " + str(x.shape))
x.reshape(2, -1).T

```

    >
       x's shape is (2, 3)
       Out[49]:
       array([[1, 4],
              [2, 5],
              [3, 6]])


To represent color images, the red, green and blue channels (RGB) must be specified for each pixel, and so the pixel value is actually a vector of three numbers ranging from 0 to 255.
One common preprocessing step in machine learning is to center and standardize your dataset, meaning that you substract the mean of the whole numpy array from each example, and then divide each example by the standard deviation of the whole numpy array. But for picture datasets, it is simpler and more convenient and works almost as well to just divide every row of the dataset by 255 (the maximum value of a pixel channel).

    >
     Common steps for pre-processing a new dataset are:
     * Figure out the dimentions and shapes of the problem (m_train, m_test,
       num_px, ...)
     * Reshape the datasets such that each example is now a vector of size
       (num_px * num_px * 3, 1)
     * "Standardize" the data

### The main steps for building a Neural Network are:

1. Define the model structure (such as number of input features)
2. Initialize the model's parameters
3. Loop:
      * Calculate current loss (forward propagation)
      * Calculate current gradient (backward propagation)
      * Update parameters (gradient descent)

You often build 1-3 separately and integrate them into one function we call
model()


\\[ \mathbf{X} = \mathbf{Z} \mathbf{P^\mathsf{T}} \\]
