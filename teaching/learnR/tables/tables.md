Tables in R
================

In this walk-through, we’ll learn the following key concepts and R
functions:

  - Using tables to estimate relatively simple probabilities (including
    conditional and joint probabilities)  
  - Making tables with `xtabs`  
  - Modifying tables with `prop.table` and `addmargins`  
  - piping (`%>%`) as a way of chaining together computations

## The data

Before you get started, download
[aclfest.csv](http://jgscott.github.io/teaching/data/aclfest.csv), which
contains data on some bands that played at several major U.S. music
festivals (including our own ACL Fest here in Austin).

## Getting started

We’ll first load the [tidyverse library](https://www.tidyverse.org/),
which we’ll need for just about every R walkthrough in this course.

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.4     ✓ dplyr   1.0.2
    ## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
    ## ✓ readr   1.4.0     ✓ forcats 0.5.0

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

If you see a similar set of messages to what’s shown above after the
`##` symbols, you’re good to go\!

But if you haven’t installed the `tidyverse` library, executing this
command will give you an error like this:

    Error in library(tidyverse) : there is no package called ‘tidyverse’

To avoid the error, you’ll first need to install `tidyverse` using the
`Install` button under the Packages tab, which is typically located in
the bottom-right panel of RStudio’s four-panel layout. Remember, an R
library is like an app on your phone: you have to install it just once,
but you have to load it each time you want to use it. On a phone, you
load an app by clicking on its icon; in R, you load a library using the
`library` command.

Next, use the `Import Dataset` button to read in the data you
downloaded, which you can find under the `Environment` tab in the
top-right panel. Follow the prompts to import `aclfest.csv`.

For a reminder on how to accomplish these two key steps (loading a
library, importing a data set), see the previous walkthrough on [Getting
Started in R]().

If you’re imported the data correctly, you can run the `head` function
to get the first six lines of the file. You should see the following
result:

``` r
head(aclfest)
```

    ##                          band acl bonnaroo coachella lollapalooza outsidelands
    ## 1                         ALO   0        0         0            0            1
    ## 2                     Battles   0        1         1            1            0
    ## 3                    Bon Iver   0        0         0            0            1
    ## 4              Flogging Molly   0        0         1            1            0
    ## 5 Ivan Neville's Dumpstaphunk   0        0         0            0            1
    ## 6                   Radiohead   0        0         0            1            1
    ##   year
    ## 1 2008
    ## 2 2008
    ## 3 2008
    ## 4 2008
    ## 5 2008
    ## 6 2008

Each entry is either a 1 or a 0, meaning “yes” and “no”, respectively.
So, for example, on the 6th line we see an entry of 1 for Radiohead
under the `lollapalooza` column, which means that Radiohead played at
Lollapalooza that year.

Let’s make a few simple tables with this data. This will allow us to
estimate probabilities like:

1.  How likely is a band in this sample to play Lollapalooza?  
2.  How like is a band to play either ACL Fest *and* Lollapalooza?
3.  How likely is a band to play ACL Fest, given that they played
    Lollapalooza?

## Simple probabilities from tables

Let’s address question 1: what is P(played Lollapalooza) for a randomly
selected band in this sample? To answer this, we’ll use R’s `xtabs`
function to tabulate (i.e. count) the data according to whether a band
played Lollapalooza (1) or not (0).

``` r
xtabs(~lollapalooza, data=aclfest)
```

    ## lollapalooza
    ##   0   1 
    ## 800 438

(You might be curious about the little tilde (`~`) symbol in front of
`lollapalooza`. Roughly speaking, `~` means “by” or “according to”—as
in, “cross-tabulate BY the `lollapalooza` variable.”)

OK, back to the table. Remember that 1 means yes and 0 means no. So of
the 1238 bands (800 + 438) in this sample, 438 of them played
Lollapalooza. We can now just use R as a calculator to get this
proportion:

``` r
438/(800 + 438)
```

    ## [1] 0.3537964

### Using prop.table

Simple enough, right? But we can also get R to turn those counts into
proportions for us, using the `prop.table` function. In general, the
more work we can get R to do for us, the better\!

One way to do this is as follows. First we’ll make the same table as
before, except now we’ll save the result in an “object” whose name we
get to choose. We’ll just call it `t1` in the code below, although you
could call it something more imaginative, like
`my_great_lollapalooza_table`, if you wanted to.

``` r
t1 = xtabs(~lollapalooza, data=aclfest)
```

Notice that nothing gets printed to the screen when you execute this
command. But if you ask R what `t1` is, it will show you the same table
as before:

``` r
t1
```

    ## lollapalooza
    ##   0   1 
    ## 800 438

OK, so why did we bother to store this table in something called `t1`?
Well, remember one of the core ideas in programming: power comes from
linking computations together. That’s exactly what we’re doing here:
we’ll take this `t1` object we’ve created (the first link our chain)
and pass it into the `prop.table` function (the second link in our
chain). This function turns a table of counts (like `t1`) into a table
of proportions, like this:

``` r
prop.table(t1)
```

    ## lollapalooza
    ##         0         1 
    ## 0.6462036 0.3537964

Notice how the answer we get—P(plays Lollapalooza) = 0.354—is the same
one we got when we did the division “by hand” (i.e. treating R as a
calculator to calculate 438/1238).

### Using pipes

The above way of doing things—using `xtabs` creating an “intermediate”
object called `t1` and then passing `t1` into the `prop.table`
function—works just fine. Lots of people write R code this way.

But it turns out there’s a nicer way to accomplish the same task, using
a “pipe” (`%>%`). Here’s how it works:

``` r
xtabs(~lollapalooza, data=aclfest) %>%
  prop.table
```

    ## lollapalooza
    ##         0         1 
    ## 0.6462036 0.3537964

This code block says: “make a table of counts of the `lollapalooza`
variable in the `aclfest` data set, and pipe (`%>%`) the resulting table
into `prop.table` to turn the table of counts into a table of
proportions.”

The result is exactly the same as before; using the pipe is totally
optional. But using pipes tends to make your code easier to easier to
write, easier to read, and easier to modify. For a simple calculation
like this involving only two steps, the difference is minimal. But for
the more complex kinds of calculations we’ll see later in the course,
the difference can be substantial.

Piping makes it especially easy to add steps in your pipeline. For
example, suppose we wanted to round all numbers to 3 decimal places, to
make these easier to read. This is easily done by adding one more pipe
to the code block above:

``` r
xtabs(~lollapalooza, data=aclfest) %>%
  prop.table %>%
  round(3)
```

    ## lollapalooza
    ##     0     1 
    ## 0.646 0.354

The table looks a lot nicer now.

For reasons of readability and ease of modification, I strongly
recommend learning to write R code using pipes.

## Joint probabilities from tables

Let’s now look at a second question: how like is a band to play either
ACL Fest *and* Lollapalooza?

We’ll start off by tabulating the bands according to both of the
relevant variables: whether they played at ACL (0 or 1), and whether
they played at Lollapalooza (0 or 1). This works as follows:

``` r
xtabs(~acl + lollapalooza, data=aclfest)
```

    ##    lollapalooza
    ## acl   0   1
    ##   0 719 361
    ##   1  81  77

Here you should interpret the `+` sign as meaning “and”, not in terms of
arithmetic. So in English, the code is saying: “cross-tabulate the
aclfest data by the `acl` AND `lollapalooza` variables.”

The result of calling `xtabs` gives us some numbers that represent joint
frequencies. Remember, for the row and column labels, 1 means yes and 0
means no. So to be specific, the output is telling us that, of the 1238
bands in the data set:

  - 719 played at neither ACL nor Lollapalooza.  
  - 81 played at ACL but not Lollapalooza.
  - 361 played at Lollapalooza but not ACL.  
  - 77 played at both ACL and Lollapalooza.

From here, we could actually stop and use use a calculator (or use R
*as* a calculator) to work out the relevant joint probability. But as
you’ll see, it’s less labor-intensive if we get R to do some of that
work for us.

So instead, let’s use `prop.table` again, to turn those counts into
proportions. As with the first example above, we’ll do this using a
pipe:

``` r
xtabs(~acl + lollapalooza, data=aclfest) %>%
  prop.table %>%
  round(3)
```

    ##    lollapalooza
    ## acl     0     1
    ##   0 0.581 0.292
    ##   1 0.065 0.062

These numbers now represent **proportions**, not counts. Therefore, all
the entries in the table sum to 1, rather than to 1238. So to be
specific, the table is telling us that:

  - P(not ACL, not Lollapalooza) = 0.581.  
  - P(not ACL, Lollapalooza) = 0.292
  - P(ACL, not Lollapalooza) = 0.065
  - P(ACL, Lollapalooza) = 0.062

And this last number, of course, is the answer to the question we set
out to answer: P(ACL, Lollapalooza) = 0.062.

### Conditional probabilities

Let turn to our final question: what is P(played ACL | played
Lollapalooza)? Or said in English, what is the conditional probability
that a band played ACL, given that they played Lollapalooza?

To answer this using the data, we’ll again tabulate the bands by both
the `acl` and `lollapalooza` variables:

``` r
xtabs(~acl + lollapalooza, data=aclfest)
```

    ##    lollapalooza
    ## acl   0   1
    ##   0 719 361
    ##   1  81  77

From this table of counts, it’s fairly straightforward to reason as
follows:

  - There were 361 + 77 = 438 bands that played at Lollapalooza.  
  - Of those 438 bands, 77 played at ACL.  
  - Therefore, P(played ACL | played Lollapalooza) = 77/438.

Let’s use R as a calculator to express this as a decimal number:

``` r
77/438
```

    ## [1] 0.1757991

So about 0.176.

### Using `margin`

But as with the previous two examples, it’s much more satisfying to get
R to do the work for us. We’ll do this using `prop.table` again—but this
time, with a twist. Pay close attention to the middle line, where we
call `prop.table`:

``` r
xtabs(~acl + lollapalooza, data=aclfest) %>%
  prop.table(margin=2) %>%
  round(3)
```

    ##    lollapalooza
    ## acl     0     1
    ##   0 0.899 0.824
    ##   1 0.101 0.176

Notice how we added `margin=2` inside parentheses in the `prop.table()`
step. What `margin=2` does is to tell `prop.table` that it should
calculate proportions **conditional on the second variable** we named,
which was `lollapalooza`. (Hence `margin=2`; if you wanted to condition
on the first variable you named, which here is `acl`, you’d type
`margin=1` instead.)

So having specified that we want to condition on the `lollapalooza`
variable, now we can read off the relevant conditional probabilities
directly from the table—no calculator required. Let’s focus on the
`lollapalooza = 1` column, since this is what we want to condition on
(i.e. that a band played Lollapalooza). The numbers in that column tell
us that:

  - P(didn’t play ACL | played Lollapalooza) = 0.824  
  - P(played ACL | played Lollapalooza) = 0.176

And that second number is precisely the answer we were going for.
