
## About the model: FAQ

### What does the model do?

We use daily data on COVID-19 deaths, together with mobile-phone data that allows us to characterize each state's social-distancing behavior, to form three-week-ahead projections of COVID-19 death rates in all US states and most major metropolitan areas. 

### What data sources do you use?

To train our model, we use daily mortality data aggregated by [Johns Hopkins University].  

For each US state, we use local data from mobile-phone GPS traces made
available by [SafeGraph] to quantify the changing impact of
social-distancing measures on "flattening the curve."  SafeGraph is
a data company that aggregates anonymized location data from numerous
applications in order to provide insights about physical places.


### Can you explain a bit more about how your model works?  What signals are you finding in the data?  

The model works by learning statistical associations between death rates and the timing/extent of social distancing behavior in each state.  The data show a clear pattern: states that have been more successful in flattening the curve seem to be those whose residents practiced social distancing more aggressively, and further in advance of when their state's death rate started to rise.  

To be a bit more specific, our model is actually an ensemble of two fundamentally different modeling strategies, both of which use mobility data to inform projections.  

Model 1 is a "curve-fitting" approach, where we fit a regression model for daily death rates versus mobility data, and then make extrapolations from that model.  This type of model is _not_ an epidemiological model: it makes no attempt to try to describe the process of disease transmission itself.  As a result, Model 1 is somewhat restricted in the kinds of death-rate curves it can describe.  Empirically, it seems to be effective at describing a single peak in the death rate that has been mitigated, to some degree or another, by social distancing.  But it's not built to account for multiple peaks in the death rate driven by distinct waves of COVID-19 transmission.

That's where Model 2 comes in.  To account for the potential shortcomings of Model 1, we use a second model with an underlying "epidemiological engine" -- specifically a version of the widely used[SEIR models](https://en.wikipedia.org/wiki/Compartmental_models_in_epidemiology) you might have read about.  To briefly describe this model:  
- It has compartments for Susceptible (S), Exposed (E), Infected (I), Recovered (R), and Dead (D).  (Technically the D compartment is actually a "boxcar" of two compartments, to get the correct distribution of waiting times from infection to death.)  
- The key parameter in the model we're estimating from the data is each state's (or MSA's) transition rate between S and E.  This rate is called beta.  
- We specify beta as a log-linear regression on mobility data.  In plain English, higher mobility across a city or state predicts higher levels of disease transmission---and our model attempts to estimate how _much_ higher.  


### How do you decide whether to use Model 1 or Model 2 for a given state?  

We don't decide; we let the data decide for us.  Specifically, we use an _ensemble_ or an _average_ of the two models' predictions, based on recent forecasting performance.  Here's how we do it.   
- Fit: We fit both models to each state's data, leaving out the last week's worth of data.  
- Backcast: We use each of the two fitted models to predict the most recent 7 days of held-out data.  This quantifies the recent forecasting performance of each model for that state.
- Refit, forecast, and weight: We then refit each model using all available data and compute each model's forecasts for the future.  To form the overall or average forecast, we weight the two models' forecasts according to how well they performed on the one-week backcast.  

In some states Model 1 will get a higher weight, while in others Model 2 will get a higher weight, depending on which model has done better at describing that state's recent past.  


### Why do you use two types of models?  

There are tradeoffs in each model, and we think that both types of models have a role to play in working our way through this crisis.  The advantage of Model 1 is that it does not require estimates of critical epidemiological parameters ("beta," "R0", recovery rate, etc), some of which remain elusive.  The advantage of Model 2 is that it can project longer-term epidemiological dynamics beyond the initial wave of mitigated transmission.  A blend of the two models seems to give the best performance.  


### Why do you model deaths rather than hospitalizations or cases?

Hospitalizations data isn't broadly available, and cases data is confounded by large geographic differences in the availability of testing.  Deaths, while almost surely under-reported to some degree, are generally viewed as being a more reliable data set.


### I've noticed that some of the error bars on your forecasts are pretty wide.  Why is that?

Mostly because future death rates really are uncertain in a lot of areas.  The error bars from our model are going to be wide in any state or city where the available data on deaths, together with the observed timing and extent of social distancing patterns, cannot rule out future growth in the death rate.

There are a couple of other points to consider here.  First, our model does err a bit on the side of conservatism: on historical back-tests, our model's 95% error bars tend to cover a little more than 95% of the notionally "future" data points. 

Second, there are a lot of things our model doesn't "know" about.  In particular, it doesn't use recent data on cases (because the data are difficult to interpret in light of variation in testing availability) or hospitalizations (because the data aren't widely available).  So we'd encourage you to think about our projections as representing a range of plausible scenarios in the absence of any knowledge of recent case/hospitalization trends in your area.  If you know, for example, that hospitalizations in your area have flattened off, then you're likely to see death rates more on the lower end of our error bars.  If hospitalizations are still growing, you're more likely to see death rates on the upper end of (or even beyond) our error bars.  




### Where can I find more details?

We use a Bayesian multilevel negative binomial regression model for the number of deaths each day, and we implement the model in R using the [`rstanarm`][rstanarm] package.  You can read all about it in our [technical report].



[Johns Hopkins University]: https://github.com/CSSEGISandData/COVID-19  
[nytimes]: https://github.com/nytimes/covid-19-data
[consortium]: https://covid-19.tacc.utexas.edu/
[SafeGraph]: https://www.safegraph.com/
[forecasts]: https://covid-19.tacc.utexas.edu/projections/
[technical report]: https://covid-19.tacc.utexas.edu/media/filer_public/87/63/87635a46-b060-4b5b-a3a5-1b31ab8e0bc6/ut_covid-19_mortality_forecasting_model_latest.pdf
[rstanarm]: https://mc-stan.org/users/interfaces/rstanarm


### Should I be concerned about data privacy?

The mobile phone data comes to us a highly anonymized, aggregated format -- we never see anything at all resembling data from an individual person's mobile phone.  Instead, we see data like: how many mobile devices visited Quack's coffee shop in Austin, TX on each day over the last three months?  We see similar numbers for all cities and most points of interest (restaurants, parks, grocery stores, pharmacies, etc) in a city.  But there's nothing in our data that could "connect the dots" of these visits and link them back to a specific device.   To further enhance privacy, SafeGraph excludes census block group information if
fewer than five devices visited an establishment in a month from a
given census block group. 
