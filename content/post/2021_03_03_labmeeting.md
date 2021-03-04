---
title: "Lab Meeting"
subtitle: "Fixed and Random effects"
author: Gavin
date: March 02, 2021
output:
  revealjs::revealjs_presentation:
    transition: slide
    includes:
      before_body: header.html
---

## Different types of _effect_


```r
lm(y ~ treatment + batch)
design(dds) <- ~ treatment + batch
```

- `treatment` can be one of a pre-chosen set of options
- `batch` takes arbitrary labels that could go on indefinitely


## Random and Fixed effects

- For `treatment` we're interested in expected values (of `treated`, and
  `control` and their difference). **FIXED** effect

- For `batch` we're less interested in the individual expected
  values. We probably assume that they're zero on average, but might
  be interested in their variance - how much 'noise' they're
  introducing. **RANDOM** effect
  
## Notation


```r
lm(y ~ treatment + batch)
library(lme4)
lmer(y ~ treatment + (1|batch))
```

These are pretty much equivalent, the latter saying 'allow a different
intercept (constant term) _for each_ batch'.

The former would phrase results in terms of individual treatment
effects and individual batch effects.

The latter would make the 'noise due to batch' the primary piece of
information (about batch, treatment is as usual).

## Quick win

Different batches have different uptake of treatment


```r
lm(y ~ treatment * batch)
lmer(y ~ treatment + (treatment|batch))
```

- First model's `treatment` coefficient is treatment effect in the
	(arbitrary) `batch₀`
	
- Second model's `treatment` coefficient is for the 'average' batch.

## Generalises

Most experiments have same number of samples (fastq's) and biological units
(mice). But frequently this is not the case.

- Left and right half of brain

- Three arbitrary regions per tissue per mouse

- Patient follow-up visits.

We believe patients may vary (in baseline, and in response?). But
we're  interested in them as a whole, not individually.

## Formulae


```r
lmer(y ~ treatment + side +  (1|mouse))
# lmer(y ~ treatment + side +  (side|mouse))
lmer(y ~ treatment +  (1|mouse))
#lmer(y ~ treatment + (1|mouse/region))
lmer(y ~ gender + time*Arm + (1|patient))
#lmer(y ~ gender + rcs(time,3)*Arm + (time|patient))
```

So we still need to think about how to specify the model!

But the language helps us focus on the true role of predictors.

## Why bother

- Should let us auto-rephrase those nasty `gender:recoded_patient +
  gender:visit + gender` formulae that DESeq2 forces upon us.
  
- More 'intelligent' plots: we don't need a legend with 73 patients in
  'different' colours (but we might like to know which genes are
  patient-variable.)
  
- Don't suggest fitting the models using `lme4::` (you need lots of
  instances, each with lots of measurements) - but it might be a handy
  sanity check occasionally.


## Package of the week

`gt` and `gtsummary` - like ggplot, but for tables. **Static** table
generation in rmarkdown, with pipe-able formatting, sorting and
margins.

`gtsummary` gives a 'Table 1' summary of RCT/observational data to
give a quick overview of the properties of a data-frame, perhaps
stratified by a subset of columns.  

It also can give the much-maligned 'Table 2' assessing the association
of each independent variable on the outcome. Don't 'conclude' anything
from these.
