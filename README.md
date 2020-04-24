
# UT-Austin forecast for COVID-19 mortality in the US

Daily COVID-19 mortality projections at state and national levels from
the [UT-Austin COVID-19 Modeling Consortium][consortium].

## Interactive display with daily updates

[You can see interactive display of our daily forecasts on our website][forecasts]

## Contents

- `forecasts/` contains our latest mortality forecasts for states and
  the US.
- `forecasts/archive/` contains daily snapshot forecasts of mortality
  from past days.

## About the model: FAQ

### What does the model do?

We use daily data on COVID-19 deaths, together with mobile-phone data that allows us to characterize each state's social-distancing behavior, to form three-week-ahead projections of COVID-19 death rates in all US states and most major metropolitan areas. 

### What data sources do you use?

To train our model, we use daily mortality data aggregated by the [New
York Times][nytimes].

For each US state, we use local data from mobile-phone GPS traces made
available by [SafeGraph] to quantify the changing impact of
social-distancing measures on "flattening the curve."  SafeGraph is
a data company that aggregates anonymized location data from numerous
applications in order to provide insights about physical places.

### Can you explain a bit more about how your model works?  What signals are you finding in the data?  

The model works by learning statistical associations between death rates and the timing/extent of social distancing behavior in each state.  At a high level, the data show a clear pattern: states that have been more successful in flattening the curve seem to be those whose residents practiced social distancing more aggressively, and further in advance of when their state's death rate started to rise.  Our model learns the quantitative details of that relationship and uses it to extrapolate probable future death rates from currently observed death rates.  

### Can your model tell us what would happen if social-distancing measures were relaxed?

No.  Our model explicitly assumes that social distancing behavior remains at the levels we've observed over the last seven days of data.  If that doesn't happen, you can throw our model's projections out the window beyond about ten days in the future.  Why ten days?  Because that's when we'd expect to see the very first deaths occurring if a renewed wave of transmisssion started today.  The vast majority of all deaths that will happen over the next ten days will happen to people who've already been infected, implying that all the relevant social-distancing behavior over that time frame has already been observed.

A bit more detail on this point: our model is a purely statistical "curve-fitting" approach, not an epidemiological model that tries to describe the process of disease transmission itself.  As a result, our model somewhat restricted in the kinds of death-rate curves it can describe.  Empirically, it seems to be effective at describing a single peak in the death rate that has been mitigated, to some degree or another, by social distancing.  But it cannot account for multiple peaks in the death rate driven by distinct waves of COVID-19 transmission.  To predict what would happen across multiple waves of transmission -- the kind of pattern that epidemiologists would expect to see if/when social-distancing measures are relaxed -- you really need a model with an underlying "epidemiological engine," such as the [SEIR models](https://en.wikipedia.org/wiki/Compartmental_models_in_epidemiology) you might have read about.  


### So why don't you use a epidemiological/SIR model instead?

There are tradeoffs, and we think that both types of models have a role to play in working our way through this crisis.  The advantage of our approach is that it does not require estimates of critical epidemiological parameters ("beta," "R0", recovery rate, etc), some of which remain elusive.  The disadvantage, as we've described above, is that it cannot project longer-term epidemiological dynamics beyond the initial wave of mitigated transmission.  For this reason, we do not use the model to make projections beyond a moderate (2-3 week) horizon.


### Why do you model deaths rather than hospitalizations or cases?

Hospitalizations data isn't broadly available, and cases data is confounded by large geographic differences in the availability of testing.  Deaths, while almost surely under-reported to some degree, are generally viewed as being a more reliable data set.  


## Where can I find more details?

If you want to get technical, we use a Bayesian multilevel negative binomial regression model for the number of deaths each day, and we implement the model in R using the [`rstanarm`][rstanarm] package.  For more details, see our [technical report].



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