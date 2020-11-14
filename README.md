
# shmanipulate

<!-- badges: start -->

<!-- badges: end -->

Easily manipulate a plot using controls like sliders, drop-down lists and date pickers.

There are other packages for doing this, like [**manipulate**](https://www.rdocumentation.org/packages/manipulate/versions/1.0.1)
and [**manipulateWidget**](https://cran.r-project.org/web/packages/manipulateWidget/vignettes/manipulateWidgets.html). I found that
with **manipulate** I would sometimes run out of space for controls when there were lots of variables; **manipulateWidget** was better 
for this, but for both packages I found the syntax a bit hard to remember, especially during what I found to be the most common use 
cases for manipulating plots, i.e. diagnosing errors and trying out some new code (when I didn't want to be diving into documentation).

**shmanipulate**'s syntax is supposed to be a bit easier to remember. The package only exports one function* (also called `shmanipulate`), 
and uses what I find to be a natural "shorthand" syntax for specifying controls for plot manipulation, e.g. `x = c(0, 1)` to control 
the variable `x` using a numeric slider going from 0 to 1, or `y = list("dog", "cat")` to control the variable `y` using a dropdown 
list with two options. The basic syntax looks like this:

``` r
shmanipulate({
   # plotting code goes here...
   }, x = c(0, 1), y = list("dog", "cat")
)
```

**shmanipulate** uses Shiny for plot manipulation, hence the name. If you want more control over plot manipulation, you can pass Shiny 
widgets to the function instead of using the "shorthand" named arguments (see below).

\* OK, I lied. **shmanipulate** also exports a second function, `shmanip`, which runs in the RStudio Viewer pane by default rather
than in a new window. And it saves five keystrokes, meaning your productivity will nearly double!

## Installation

You can install **shmanipulate** from GitHub using:

``` r
remotes::install_github("nicholasdavies/shmanipulate")
```

## Quick examples

See `?shmanipulate` for full documentation.

### Specifying controls: the easy way

Here's an example of using `shmanipulate` with a few different basic control types.

``` r
shmanipulate( { x = 0:10; plot(A * x^2 + B * x + C, col = if(blue) 4 else 1, main = plot_title, ylim = c(-5, 10)) },
    A = c(0, 0.1), # a slider from 0 to 0.1
    B = 1,         # a numeric text input with starting value 1
    C = list(one = 1, two = 2, three = 3), # a dropdown list with named values
    plot_title = "Example title", # freeform text input
    blue = FALSE                  # checkbox
)
```

In full, you can use:
- `x = c(min, max)` for a numeric slider between min and max; you can optionally provide a starting value before min and/or a step value after max (see "Different kinds of numeric sliders" below).
- `y = list(...)` for a fixed set of options in a dropdown menu. If the list has names, these will be shown; otherwise, the list values converted to character are shown. The first element is selected by default. For convenience, a vector of strings with more than one element (e.g. `c("one", "two")`) will also be interpreted as a dropdown menu. 
- `z = TRUE` or `z = FALSE` for a logical value controlled by a checkbox.
- `foo = "Some text"` for a character string controlled by text input.
- `bar = 123.456` for a numeric value controlled by text input.
- `baz = as.Date("2020-01-01")` for a Date object with a calendar input.

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

### shmanipulate + curve: a delicious pairing

Base R provides the function `curve` to quickly plot the value of an expression over a range of values in a variable `x`.
This can be paired with `shmanipulate` for a quick way of familiarising yourself with how a function behaves.

``` r
shmanipulate(curve(dbeta(x, alpha, beta), 0, 1), alpha = c(1, 100), beta = c(1, 100))
```

### Maybe you like histograms?

``` r
data(quakes)
shmanipulate(if (x == y) hist(quakes[[x]], xlab = x) else plot(quakes[[x]], quakes[[y]], xlab = x, ylab = y), 
    x = names(quakes), y = names(quakes))
```
