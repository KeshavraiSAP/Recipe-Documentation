---
title: Own Your Documentation
description: Own Your Documentation.
---

# Adding your recipe document
## NOTE
    - This applies to Recipe Owners and UA.
    - This section explains the process for adding a Recipe.

## *Pitch*
- You have a technical need to automate a scope, then pitch in your idea by filling <a href="../files/recipe-opfs.xlsx" download>One-pager Fact sheet</a>.
- Once filled, contact [us](../be-a-contributor/contact.md).
- Let's meet and talk about Portfolio Fit, Prioritization, Scope, timeline.

## *Define*
- Recipe UA authors the recipe using this <a href="../files/recipe_content.xlsx" download>format</a>.
- Recipe UX Designer creates the recipe mock screens.
- Recipe implementation team evaluates the required api and accessibility.


## Things to consider
### *Recipe Phases*
Recipe supports different visibility for controlling the recipe availability in different landscape. Below Table explains each combination:

| Visibility | Is Trial Supported | Status                | Productive Landscape          | Trial                                             |
| ---------- | ------------------ | --------------------- | ----------------------------- | ------------------------------------------------- |
| Private    | False              | Draft, Beta, Released | Dev                           |                                                   |
| Private    | True               | Draft, Beta, Released | Dev                           | Dev CF Trial                                      |
| Public     | False              | Draft                 | Dev                           |                                                   |
| Public     | True               | Draft                 | Dev                           | Dev CF Trial                                      |
| Public     | False              | Beta                  | Dev, Staging, Canary          |                                                   |
| Public     | True               | Beta                  | Dev, Staging, Canary          | Dev CF Trial, Staging CF Trial                    |
| Public     | False              | Released              | Dev, Staging, Canary, Factory |                                                   |
| Public     | True               | Released              | Dev, Staging, Canary, Factory | Dev CF Trial, Staging CF Trial , Factory CF Trial |


### *Image Content*
Below guidelines needs to be followed for images:

1. Image file names should be context based and relates to the image.
2. Image should have transparent background.
3. Image needs to be compressed/optimized for network calls.
4. Image should be less prone to getting outdated in a short time. For example, an image of app and app changes frequently, then we have maintenance overhead.


### *Additional Links*
The links provided needs to be working all the time. If the link is about to change, then you need to notify [us](../be-a-contributor/contact.md) in advance or do the redirection of old link to the new updated link.
