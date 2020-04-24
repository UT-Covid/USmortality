
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

### Can your model tell us what would happen if social-distancing measures were relaxed?

No.  Our model explicitly assumes that social distancing behavior remains at the levels we've observed over the last seven days of data.  If that doesn't happen, you can throw our model's projections out the window.  

The reason for this is that our model is a "curve-fitting" approach designed to describe a single wave of deaths that has been mitigated by social distancing.  To predict what would happen across multiple waves of COVID-19 transmission -- the kind of pattern we'd expect to see -- you really need a model with an underlying "epidemiological engine."....


### Can you explain a bit more about how the model works?

The model works by learning statistical associations between death rates and the timing/extent of social distancing behavior in each state.  At a high level, the data show a clear pattern: states that have been more successful in flattening the curve seem to be those whose residents practiced social distancing more aggressively, and further in advance of when their state's death rate started to rise.  Our model learns the quantitative details of that relationship and uses it to extrapolate probable future death rates. 


### Why do you use deaths rather than 



## For more details

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