

# TFX paper
[TFX: A TensorFlow-Based Production-Scale Machine Learning Platform](http://www.kdd.org/kdd2017/papers/view/tfx-a-tensorflow-based-production-scale-machine-learning-platform)


### Feature stats

* for continuous features, the statistics include 
    * quantiles
    * histograms
    * the mean and standard deviation
* discrete features 
    * top-K values by frequency.
* stats on configurable slices of the data
* Cross feature statistics (correlation and covariance)

On large training data, some of these statistics become difficult to compute exactly, and the component resorts to distributed streaming algorithms that give approximate results


### Validation

* are all features present in the data
* The expected type of each feature the same?
* Minimum coverage (percentage and count)• 
* range (min/max or set belonging) of possible distinct values



### MODEL TRAINING

Warm starting - 

* inspired by transfer learning, 
* initialize the “general parameters” from the previous run - and begin training on (new data + old data)
* If used correctly converges rapidly 
* specific to neural networks
* Is incremental model training a new research area?



### Model  Validation

* serialized model format changes
* bugs in the model's train/predict code that can crash the model.
* feature schema changes
* Answers the question of “will this model crash my production serving”?



### MODEL EVALUATION

* costly and time-consuming to run A/B experiments on live traffic,
*  models are evaluated offline on held-out data for a live experiment
* compute AUC or cost-weighted error offline 
    * alerts on changes to these metrics
    * threshold for alerting too low - users will ignore
    * threshold for alerting too high - will catch the obvious bugs, will fail to report subtle ones
* ability to compute metrics on slices of data
    * “a product team might be concerned about the performance of their model in the US”
    * “avoid serving models that sacrifice quality on these slices for better overall performance.”
* users don't like adding validation checks - too much work for no immediate gain
    * “we plan to provide a configuration-free validation setup that is enabled by default for all users”



### Model Serving

“low latency, high efficiency, horizontal scalability, reliability and robustness”

* Isolation
    * “soft-model isolation“ - performance characteristics of one model has minimal impact on other models
    * “observed latency peaks during the interval when the system was loading a new model“
        * dedicated threadpool for model-loading operations
            * p99 during model loading went from (500 - 1500) msec  to (75 - 150) msec
* Non neural network (e.g., linear, trees) models are often more data intensive than CPU intensive.
    * super optimized input parsing 
        * specific to data types and data distributions 
        *  uses protocol buffer
        * minimize data copying
        * 2-5x speedup



### Case study: Google play ranking system

* Increased iteration speed
* reduced tech debt
* Increased model quality
* Specifically:
    * Discovered a training serving feature skew 
        *  a feature was always absent while serving
        * 2% lift
    * Warm starting - cut down training time, models can be fresher, and pushed more frequently
    * Model evaluation - caught bad model(partially trained model) about to be pushed



### Thoughts on Bighead

Hardware and the underlying ml libraries are evolving very rapidly - to speed up inference and training. Spending time on optimization might be very low leverage in the long term.

Sanity checking models and data might seem low leverage, but will users save a ton of time debugging, in the long term.

Model evaluation is a glaring hole in the bighead story. 

