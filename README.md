
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

## About the model

### Daily deaths data by state

To train our model, we use daily mortality data aggregated by the [New
York Times][nytimes].

### Social distancing metrics

For each US state, we use local data from mobile-phone GPS traces made
available by [SafeGraph] to quantify the changing impact of
social-distancing measures on ``flattening the curve.''  SafeGraph is
a data company that aggregates anonymized location data from numerous
applications in order to provide insights about physical places. To
enhance privacy, SafeGraph excludes census block group information if
fewer than five devices visited an establishment in a month from a
given census block group.

We use a Bayesian multilevel negative binomial regression model for
the number of deaths each day, and implement the model in R using
the [`rstanarm`][rstanarm] package.

## Technical report

For more details, see our [technical report].


[nytimes]: https://github.com/nytimes/covid-19-data
[consortium]: https://covid-19.tacc.utexas.edu/
[SafeGraph]: https://www.safegraph.com/
[forecasts]: https://covid-19.tacc.utexas.edu/projections/
[technical report]: https://covid-19.tacc.utexas.edu/media/filer_public/87/63/87635a46-b060-4b5b-a3a5-1b31ab8e0bc6/ut_covid-19_mortality_forecasting_model_latest.pdf
[rstanarm]: https://mc-stan.org/users/interfaces/rstanarm
