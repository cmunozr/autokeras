R interface to Auto-Keras
================

[![Travis-CI Build
Status](https://travis-ci.org/jcrodriguez1989/autokeras.svg?branch=dev)](https://travis-ci.org/jcrodriguez1989/autokeras)
[![Coverage
status](https://codecov.io/gh/jcrodriguez1989/autokeras/branch/dev/graph/badge.svg)](https://codecov.io/gh/jcrodriguez1989/autokeras/branch/dev)
[![lifecycle](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)

[Auto-Keras](https://autokeras.com/) is an open source software library
for automated machine learning (AutoML). It is developed by [DATA
Lab](http://faculty.cs.tamu.edu/xiahu/index.html) at Texas A\&M
University and community contributors. The ultimate goal of AutoML is to
provide easily accessible deep learning tools to domain experts with
limited data science or machine learning background. Auto-Keras provides
functions to automatically search for architecture and hyperparameters
of deep learning models.

Check out the [Auto-Keras blogpost at the **RStudio TensorFlow for R
blog**](https://blogs.rstudio.com/tensorflow/posts/2019-04-16-autokeras/).

## Dependencies

  - [Auto-Keras](https://autokeras.com/) requires Python 3.6 .

## Installation

AutoKeras is currently only available as a GitHub package. To install it
run the following from an R console:

``` r
if (!require("remotes")) {
  install.packages("remotes")
}
remotes::install_github("r-tensorflow/autokeras")
```

Then, use the `install_autokeras()` function to install TensorFlow:

``` r
library("autokeras")
install_autokeras()
```

## Examples

### CIFAR-10 dataset

``` r
library("keras")

# Get CIFAR-10 dataset, but not preprocessing needed
cifar10 <- dataset_cifar10()
c(x_train, y_train) %<-% cifar10$train
c(x_test, y_test) %<-% cifar10$test
```

``` r
library("autokeras")

# Create an image classifier, and train 10 different models
clf <- model_image_classifier(max_trials = 10) %>%
  fit(x_train, y_train)
```

``` r
# And use it to evaluate, predict
clf %>% evaluate(x_test, y_test)
```

``` r
clf %>% predict(x_test[1:10, , , ])
```

``` r
# Get the best trained Keras model, to work with the keras R library
export_model(clf)
```

### IMDb dataset

``` r
library("keras")

# Get IMDb dataset
imdb <- dataset_imdb(num_words = 1000)
c(x_train, y_train) %<-% imdb$train
c(x_test, y_test) %<-% imdb$test

# Auto-Keras procceses each text data point as a character vector,
# i.e., x_train[[1]] "<START> this film was just brilliant casting..",
# so we need to transform the dataset.
word_index <- dataset_imdb_word_index()
word_index <- c(
  "<PAD>", "<START>", "<UNK>", "<UNUSED>",
  names(word_index)[order(unlist(word_index))]
)
x_train <- lapply(x_train, function(x) {
  paste(word_index[x + 1], collapse = " ")
})
x_test <- lapply(x_test, function(x) {
  paste(word_index[x + 1], collapse = " ")
})

x_train <- array(unlist(x_train))
x_test <- array(unlist(x_test))
y_train <- matrix(y_train, ncol = 1)
y_test <- matrix(y_test, ncol = 1)
```

``` r
library("autokeras")

# Create a text classifier, and train 10 different models
clf <- model_text_classifier(max_trials = 10) %>%
  fit(x_train, y_train)
```

``` r
# And use it to evaluate, predict
clf %>% evaluate(x_test, y_test)
```

``` r
clf %>% predict(x_test[1:10])
```

``` r
# Get the best trained Keras model, to work with the keras R library
export_model(clf)
```
