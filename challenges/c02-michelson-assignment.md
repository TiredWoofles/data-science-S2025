Michelson Speed-of-light Measurements
================
Suwanee Li
2025-02-12

- [Grading Rubric](#grading-rubric)
  - [Individual](#individual)
  - [Submission](#submission)
    - [**q1** Re-create the following table (from Michelson (1880),
      pg. 139) using `df_michelson` and `dplyr`. Note that your values
      *will not* match those of Michelson *exactly*; why might this
      be?](#q1-re-create-the-following-table-from-michelson-1880-pg-139-using-df_michelson-and-dplyr-note-that-your-values-will-not-match-those-of-michelson-exactly-why-might-this-be)
    - [**q2** Create a new variable `VelocityVacuum` with the $+92$ km/s
      adjustment to `Velocity`. Assign this new dataframe to
      `df_q2`.](#q2-create-a-new-variable-velocityvacuum-with-the-92-kms-adjustment-to-velocity-assign-this-new-dataframe-to-df_q2)
    - [**q3** Compare Michelson’s speed of light estimate against the
      modern speed of light value. Is Michelson’s estimate of the error
      (his uncertainty) greater or less than the true
      error?](#q3-compare-michelsons-speed-of-light-estimate-against-the-modern-speed-of-light-value-is-michelsons-estimate-of-the-error-his-uncertainty-greater-or-less-than-the-true-error)
    - [**q4** Inspect the following plot with the `Real` Michelson data
      and `Simulated` data from a probability model. Document the
      similarities and differences between the data under *observe*
      below.](#q4-inspect-the-following-plot-with-the-real-michelson-data-and-simulated-data-from-a-probability-model-document-the-similarities-and-differences-between-the-data-under-observe-below)
    - [**q5** You have access to a few other variables. Construct a **at
      least three** visualizations of `VelocityVacuum` against these
      other factors. Are there other patterns in the data that might
      help explain the difference between Michelson’s estimate and
      `LIGHTSPEED_VACUUM`?](#q5-you-have-access-to-a-few-other-variables-construct-a-at-least-three-visualizations-of-velocityvacuum-against-these-other-factors-are-there-other-patterns-in-the-data-that-might-help-explain-the-difference-between-michelsons-estimate-and-lightspeed_vacuum)
  - [Bibliography](#bibliography)

*Purpose*: When studying physical problems, there is an important
distinction between *error* and *uncertainty*. The primary purpose of
this challenge is to dip our toes into these factors by analyzing a real
dataset.

*Reading*: [Experimental Determination of the Velocity of
Light](https://play.google.com/books/reader?id=343nAAAAMAAJ&hl=en&pg=GBS.PA115)
(Optional)

<!-- include-rubric -->

# Grading Rubric

<!-- -------------------------------------------------- -->

Unlike exercises, **challenges will be graded**. The following rubrics
define how you will be graded, both on an individual and team basis.

## Individual

<!-- ------------------------- -->

| Category | Needs Improvement | Satisfactory |
|----|----|----|
| Effort | Some task **q**’s left unattempted | All task **q**’s attempted |
| Observed | Did not document observations, or observations incorrect | Documented correct observations based on analysis |
| Supported | Some observations not clearly supported by analysis | All observations clearly supported by analysis (table, graph, etc.) |
| Assessed | Observations include claims not supported by the data, or reflect a level of certainty not warranted by the data | Observations are appropriately qualified by the quality & relevance of the data and (in)conclusiveness of the support |
| Specified | Uses the phrase “more data are necessary” without clarification | Any statement that “more data are necessary” specifies which *specific* data are needed to answer what *specific* question |
| Code Styled | Violations of the [style guide](https://style.tidyverse.org/) hinder readability | Code sufficiently close to the [style guide](https://style.tidyverse.org/) |

## Submission

<!-- ------------------------- -->

Make sure to commit both the challenge report (`report.md` file) and
supporting files (`report_files/` folder) when you are done! Then submit
a link to Canvas. **Your Challenge submission is not complete without
all files uploaded to GitHub.**

``` r
# Libraries
library(tidyverse)
library(googlesheets4)

url <- "https://docs.google.com/spreadsheets/d/1av_SXn4j0-4Rk0mQFik3LLr-uf0YdA06i3ugE6n-Zdo/edit?usp=sharing"

# Parameters
LIGHTSPEED_VACUUM    <- 299792.458 # Exact speed of light in a vacuum (km / s)
LIGHTSPEED_MICHELSON <- 299944.00  # Michelson's speed estimate (km / s)
LIGHTSPEED_PM        <- 51         # Michelson error estimate (km / s)
```

*Background*: In 1879 Albert Michelson led an experimental campaign to
measure the speed of light. His approach was a development upon the
method of Foucault\[3\], and resulted in a new estimate of
$v_0 = 299944 \pm 51$ kilometers per second (in a vacuum). This is very
close to the modern *exact* value of 2.9979246^{5}. In this challenge,
you will analyze Michelson’s original data, and explore some of the
factors associated with his experiment.

I’ve already copied Michelson’s data from his 1880 publication; the code
chunk below will load these data from a public googlesheet.

*Aside*: The speed of light is *exact* (there is **zero error** in the
value `LIGHTSPEED_VACUUM`) because the meter is actually
[*defined*](https://en.wikipedia.org/wiki/Metre#Speed_of_light_definition)
in terms of the speed of light!

``` r
## Note: No need to edit this chunk!
gs4_deauth()
ss <- gs4_get(url)
df_michelson <-
  read_sheet(ss) %>%
  select(Date, Distinctness, Temp, Velocity) %>%
  mutate(Distinctness = as_factor(Distinctness))
```

    ## ✔ Reading from "michelson1879".

    ## ✔ Range 'Sheet1'.

``` r
df_michelson %>% glimpse()
```

    ## Rows: 100
    ## Columns: 4
    ## $ Date         <dttm> 1879-06-05, 1879-06-07, 1879-06-07, 1879-06-07, 1879-06-…
    ## $ Distinctness <fct> 3, 2, 2, 2, 2, 2, 3, 3, 3, 3, 2, 2, 2, 2, 2, 1, 3, 3, 2, …
    ## $ Temp         <dbl> 76, 72, 72, 72, 72, 72, 83, 83, 83, 83, 83, 90, 90, 71, 7…
    ## $ Velocity     <dbl> 299850, 299740, 299900, 300070, 299930, 299850, 299950, 2…

*Data dictionary*:

- `Date`: Date of measurement
- `Distinctness`: Distinctness of measured images: 3 = good, 2 = fair, 1
  = poor
- `Temp`: Ambient temperature (Fahrenheit)
- `Velocity`: Measured speed of light (km / s)

### **q1** Re-create the following table (from Michelson (1880), pg. 139) using `df_michelson` and `dplyr`. Note that your values *will not* match those of Michelson *exactly*; why might this be?

| Distinctness | n   | MeanVelocity |
|--------------|-----|--------------|
| 3            | 46  | 299860       |
| 2            | 39  | 299860       |
| 1            | 15  | 299810       |

``` r
## TODO: Compute summaries
df_michelson
```

    ## # A tibble: 100 × 4
    ##    Date                Distinctness  Temp Velocity
    ##    <dttm>              <fct>        <dbl>    <dbl>
    ##  1 1879-06-05 00:00:00 3               76   299850
    ##  2 1879-06-07 00:00:00 2               72   299740
    ##  3 1879-06-07 00:00:00 2               72   299900
    ##  4 1879-06-07 00:00:00 2               72   300070
    ##  5 1879-06-07 00:00:00 2               72   299930
    ##  6 1879-06-07 00:00:00 2               72   299850
    ##  7 1879-06-09 00:00:00 3               83   299950
    ##  8 1879-06-09 00:00:00 3               83   299980
    ##  9 1879-06-09 00:00:00 3               83   299980
    ## 10 1879-06-09 00:00:00 3               83   299880
    ## # ℹ 90 more rows

``` r
df_q1 <- df_michelson
df_q1 %>%
  group_by(Distinctness) %>%
  summarise(
    n = n(), 
    meanvelocity = mean(Velocity)) %>% 
  arrange(desc(Distinctness)) %>%
  knitr::kable()
```

| Distinctness |   n | meanvelocity |
|:-------------|----:|-------------:|
| 3            |  46 |     299861.7 |
| 2            |  39 |     299858.5 |
| 1            |  15 |     299808.0 |

**Observations**: - Write your observations here! - (Your response
here) - Why might your table differ from Michelson’s? -

- Our Distinctness and the amount of them are consistent. But the Mean
  Velocity values are not. This is likely due to how their information
  is rounded differently than the way our code calculates it.

The `Velocity` values in the dataset are the speed of light *in air*;
Michelson introduced a couple of adjustments to estimate the speed of
light in a vacuum. In total, he added $+92$ km/s to his mean estimate
for `VelocityVacuum` (from Michelson (1880), pg. 141). While the
following isn’t fully rigorous ($+92$ km/s is based on the mean
temperature), we’ll simply apply this correction to all the observations
in the dataset.

### **q2** Create a new variable `VelocityVacuum` with the $+92$ km/s adjustment to `Velocity`. Assign this new dataframe to `df_q2`.

``` r
## TODO: Adjust the data, assign to df_q2
df_q2 <- df_michelson %>%
  mutate(velocityvacuum = Velocity + 92)
df_q2
```

    ## # A tibble: 100 × 5
    ##    Date                Distinctness  Temp Velocity velocityvacuum
    ##    <dttm>              <fct>        <dbl>    <dbl>          <dbl>
    ##  1 1879-06-05 00:00:00 3               76   299850         299942
    ##  2 1879-06-07 00:00:00 2               72   299740         299832
    ##  3 1879-06-07 00:00:00 2               72   299900         299992
    ##  4 1879-06-07 00:00:00 2               72   300070         300162
    ##  5 1879-06-07 00:00:00 2               72   299930         300022
    ##  6 1879-06-07 00:00:00 2               72   299850         299942
    ##  7 1879-06-09 00:00:00 3               83   299950         300042
    ##  8 1879-06-09 00:00:00 3               83   299980         300072
    ##  9 1879-06-09 00:00:00 3               83   299980         300072
    ## 10 1879-06-09 00:00:00 3               83   299880         299972
    ## # ℹ 90 more rows

As part of his study, Michelson assessed the various potential sources
of error, and provided his best-guess for the error in his
speed-of-light estimate. These values are provided in
`LIGHTSPEED_MICHELSON`—his nominal estimate—and
`LIGHTSPEED_PM`—plus/minus bounds on his estimate. Put differently,
Michelson believed the true value of the speed-of-light probably lay
between `LIGHTSPEED_MICHELSON - LIGHTSPEED_PM` and
`LIGHTSPEED_MICHELSON + LIGHTSPEED_PM`.

Let’s introduce some terminology:\[2\]

- **Error** is the difference between a true value and an estimate of
  that value; for instance `LIGHTSPEED_VACUUM - LIGHTSPEED_MICHELSON`.
- **Uncertainty** is an analyst’s *assessment* of the error.

Since a “true” value is often not known in practice, one generally does
not know the error. The best they can do is quantify their degree of
uncertainty. We will learn some means of quantifying uncertainty in this
class, but for many real problems uncertainty includes some amount of
human judgment.\[2\]

### **q3** Compare Michelson’s speed of light estimate against the modern speed of light value. Is Michelson’s estimate of the error (his uncertainty) greater or less than the true error?

``` r
## TODO: Compare Michelson's estimate and error against the true value
## Your code here!
# Clean version of q3 output
tibble(
  error = LIGHTSPEED_VACUUM - LIGHTSPEED_MICHELSON,
  plus_bound = LIGHTSPEED_MICHELSON + LIGHTSPEED_PM,
  minus_bound = LIGHTSPEED_MICHELSON - LIGHTSPEED_PM
) %>%
  mutate(Uncertainty = plus_bound-minus_bound)
```

    ## # A tibble: 1 × 4
    ##   error plus_bound minus_bound Uncertainty
    ##   <dbl>      <dbl>       <dbl>       <dbl>
    ## 1 -152.     299995      299893         102

**Observations**: - Is Michelson’s estimate of the error (his
uncertainty) greater or less than the true error? - (Your response
here) - Make a quantitative comparison between Michelson’s uncertainty
and his error. - (Your response here)

Micheleason’s uncertainty is +- 51km/s. This is less than the true error
of 151.542 km/s. 151.542 km/s - 51km/s results in a difference of
100.542 km/s and for my comparison 151.542/52 shows a ratio of error to
uncertainty is 2.97 or the error is nearly 3 times larger than
Michelson’s estimated uncertainty.

The following plot shows all of Michelson’s data as a [control
chart](https://en.wikipedia.org/wiki/Control_chart); this sort of plot
is common in manufacturing, where it is used to help determine if a
manufacturing process is under [statistical
control](https://en.wikipedia.org/wiki/Statistical_process_control).
Each dot is one of Michelson’s measurements, and the grey line connects
the mean taken for each day. The same plot also shows simulated data
using a probability model. We’ll get into statistics later in the
course; for now, let’s focus on understanding what real and simulated
data tend to look like.

### **q4** Inspect the following plot with the `Real` Michelson data and `Simulated` data from a probability model. Document the similarities and differences between the data under *observe* below.

``` r
## Note: No need to edit this chunk!
## Calibrate simulated data
v_mean <-
  df_q2 %>%
  summarize(m = mean(velocityvacuum )) %>%
  pull(m)
v_sd <-
  df_q2 %>%
  summarize(s = sd(velocityvacuum)) %>%
  pull(s)

## Visualize
set.seed(101)
df_q2 %>%
  mutate(Simulated = rnorm(n(), mean = v_mean, sd = v_sd)) %>%
  rename(Real = velocityvacuum) %>%
  pivot_longer(
    cols = c(Simulated, Real),
    names_to = "source",
    values_to = "velocity"
  ) %>%

  ggplot(aes(Date, velocity)) +
  geom_hline(
    yintercept = LIGHTSPEED_MICHELSON,
    linetype = "dotted"
  ) +
  geom_hline(
    yintercept = LIGHTSPEED_MICHELSON - LIGHTSPEED_PM,
    linetype = "dashed"
  ) +
  geom_hline(
    yintercept = LIGHTSPEED_MICHELSON + LIGHTSPEED_PM,
    linetype = "dashed"
  ) +

  geom_line(
    data = . %>%
      group_by(Date, source) %>%
      summarize(velocity_mean = mean(velocity)),
    mapping = aes(y = velocity_mean),
    color = "grey50"
  ) +
  geom_point(
    mapping = aes(y = velocity),
    size = 0.8
  ) +

  facet_grid(source~.) +
  theme_minimal() +
  labs(
    x = "Date of Measurement (1879)",
    y = "Velocity (in Vacuum)"
  )
```

    ## `summarise()` has grouped output by 'Date'. You can override using the
    ## `.groups` argument.

![](c02-michelson-assignment_files/figure-gfm/q4-cf-real-simulated-1.png)<!-- -->

**Observations**: Similarities - (your responses here) Differences -
(your responses here)

Similar

- Both real and simulated have many measurements that fall within the
  Michelson’s margin of error (dashed lines)

- Both show random variation in measurement across the different days

- Both the grey line generally hovers around the central estimate

Differences

- Real data is more consistent to day to day averages as the mean line
  is relatively stable, suggesting real measurement conditions have more
  systematic controls

- Simulated data has more frequency and sharper day to say swings in the
  mean which reflects the random variation that Michelson’s experimental
  constraints did not have.

- There are more extreme outliers in the simulated data that exceed even
  the upper or lower bounds (shown in dashed lines). The real data
  mostly stays within those control limits

### **q5** You have access to a few other variables. Construct a **at least three** visualizations of `VelocityVacuum` against these other factors. Are there other patterns in the data that might help explain the difference between Michelson’s estimate and `LIGHTSPEED_VACUUM`?

``` r
ggplot(df_q2, aes(x = Date, y = velocityvacuum)) +
  geom_point(size = 2, alpha = 0.7) +
  geom_smooth(method = "loess", se = FALSE, color = "red") + 
  geom_hline(yintercept = LIGHTSPEED_VACUUM, linetype = "dashed", color = "blue") +
  labs(
    title = "Speed of Light Over Time",
    x = "Date of Measurement",
    y = "Velocity (in Vacuum)"
  ) +
  theme_minimal()
```

    ## `geom_smooth()` using formula = 'y ~ x'

![](c02-michelson-assignment_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
ggplot(df_q2, aes(x = factor(Distinctness), y = velocityvacuum)) +
  geom_boxplot() +
  geom_hline(yintercept = LIGHTSPEED_VACUUM, linetype = "dashed", color = "blue") +
  labs(
    title = "Speed of Light by Image Clarity",
    x = "Distinctness (3 = Good, 2 = Fair, 1 = Poor)",
    y = "Velocity (in Vacuum)"
  ) +
  theme_minimal()
```

![](c02-michelson-assignment_files/figure-gfm/unnamed-chunk-2-2.png)<!-- -->

``` r
ggplot(df_q2, aes(x = Temp, y = velocityvacuum)) +
  geom_point(size = 2, alpha = 0.7) + 
  geom_smooth(method = "loess", se = FALSE, color = "red") + 
  geom_hline(yintercept = LIGHTSPEED_VACUUM, linetype = "dashed", color = "blue") +
  labs(
    title = "Speed of Light Over Temperature",
    x = "Temp of Measurement",
    y = "Velocity (in Vacuum)"
  ) +
  theme_minimal()
```

    ## `geom_smooth()` using formula = 'y ~ x'

![](c02-michelson-assignment_files/figure-gfm/unnamed-chunk-2-3.png)<!-- -->

**Observations**:

- Make sure to record observations on your graphs!
- Graph 1 Comparing dates to the velocity in vacuum and I noticed that
  their mean velocity picks up early on before taking a dip around June
  16th. But all the values are pretty above the light speed vacuum.
  Suggest systematic bias in Micheson’s measuremnts but does not explain
  why.
- Graph 2 The Distinctness values mean don’t deviate too far from each
  other and are close in value but very far away from the light speed
  vacuum. Distinctness may not have had a large influence on velocity.
  does not explain overestimation.
- Graph 3 between temp to the mean velocities between the ranges of 70
  to 85 the values are nearly the same to each other. Outside of that
  range when the temperature is lower the velocity value gets lower.
  Then increases for higher temperatures. However, they are still way
  above the light speed vacuum. There is a weak trend with tempearure
  but its not strong or consisten enough to explain the difference.

## Bibliography

- \[1\] Michelson, [Experimental Determination of the Velocity of
  Light](https://play.google.com/books/reader?id=343nAAAAMAAJ&hl=en&pg=GBS.PA115)
  1880) 
- \[2\] Henrion and Fischhoff, [Assessing Uncertainty in Physical
  Constants](https://www.cmu.edu/epp/people/faculty/research/Fischoff-Henrion-Assessing%20uncertainty%20in%20physical%20constants.pdf)
  1986) 
- \[3\] BYU video about a [Fizeau-Foucault
  apparatus](https://www.youtube.com/watch?v=Ik5ORaaeaME), similar to
  what Michelson used.
