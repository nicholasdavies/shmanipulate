
<!-- README.md is generated from README.Rmd. Please edit that file -->

# shmanipulate

<!-- badges: start -->

<!-- badges: end -->

Easily manipulate a plot using controls like sliders, drop-down lists
and date pickers.

*shmanipulate* uses Shiny for plot manipulation, hence the name.

## Installation

You can install *shmanipulate* from GitHub with:

``` r
remotes::install_github("nicholasdavies/shmanipulate")
```

## Quick examples

See `?shmanipulate` for full
documentation.

### Specifying controls: the easy way

``` r
shmanipulate( { x = 0:10; plot(A * x^2 + B * x + as.numeric(C), col = if(blue) 4 else 1, main = plot_title, ylim = c(-5, 10)) },
    A = c(0, 0.1), # a slider from 0 to 0.1
    B = 1,         # a numeric text input with starting value 1
    C = list(one = 1, two = 2, three = 3), # a dropdown list with named values
    plot_title = "Example title", # freeform text input
    blue = FALSE                  # checkbox
)
```

### Specifying controls: the flexible way

``` r
library(shiny)
library(ggplot2)

shmanipulate({
        dat = data.frame(date = start_date + 0:(n_days - 1),
            value = start_value * exp(0:(n_days - 1) * growth_rate) + rnorm(n_days, 0, noise));
        ggplot(dat) +
            geom_line(aes(x = date, y = value))
    },
    dateInput(inputId = "start_date", label = "Start date", value = "2020-01-01"),
    numericInput(inputId = "start_value", label = "Starting value", value = 1, min = 0, max = 10, step = 1),
    sliderInput(inputId = "growth_rate", label = "Growth rate", min = 0, max = 1, value = 0, step = 0.01),
    numericInput(inputId = "n_days", label = "Number of days", value = 30, min = 1, max = 60, step = 1),
    sliderInput(inputId = "noise", label = "Noise", min = 0, max = 1, value = 0, step = 0.01)
)
```

### Different kinds of numeric sliders

``` r
shmanipulate({ x = 0:100; plot(A * x^2 + B * x + C, ylim = c(-2000, 2000)) },
    A = c(0.5, 0, 1),         # slider from 0 to 1, with starting value 0.5
    B = c(0, 10, 0.25),       # slider from 0 to 10, with step 0.25
    C = c(0, -1000, 1000, 50) # slider from -1000 to 1000, with starting value 0 and step 50
)
```
