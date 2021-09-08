---
title: "Visualizing Relative Harm and Detention"
draft: false
tags: ["projects","plotly","visualization","prison","harm"]
date: 2021-09-07
---
**Relative Harm and Pretrial Detention**

According to [the Prison Policy Institute in 2020](https://www.prisonpolicy.org/reports/pie2020.html), more than 470,000 people at any one time are sitting in local jails without a conviction. These people, along with many in federal detention, are in what is known as *pretrial detention*: they are sitting in jail awaiting the outcome of a court case. Many of these people will eventually accept plea deals - not necessarily because they are guilty, but because a guilty plea is the quickest way out. Still others will await their day in court, their lives paused for potentially months on end while the world outside - their jobs, their families, their communities - moves on without them.

It is often difficult to conceptualize the harm done to people detained by the state. In some ways, conceptualizing the harm wrought by a crime is easier: we can imagine the frustration of having our treasured belongings stolen, or the fear and pain of a violent assault. The harm caused by many crimes is familiar, and we can draw analogies to our own experiences. Most of us have never been separated entirely from society, from our lives, trapped in a box and stored away for weeks, months, or years at a time. There is no analogy for us to draw on when imagining the harm of even a short stay in jail.

Recently, I came across a very interesting paper: [Pretrial Detention and the Value of Liberty](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3787018). The authors, Megan Stevenson and Sandra Mayson of the University of Virginia School of Law and the Univerity of Pennsylvania Carey Law School respectively, published [an article in The Appeal](https://theappeal.org/virtually-no-one-is-dangerous-enough-to-justify-jail/) describing the surveys they conducted in the paper. In their research, they try to quantify the harm caused by detention using a clever strategy: by tying that harm directly to the crimes that put people in jail. 

Stevenson and Mayson prime their survey participants by asking them to imagine the worst consequences that would arise from a given crime and, similarly, the worst consequences that would arise from being detained for a length of time. Then they ask participants to estimate how many hours, days, months, or years they'd be willing to spend in jail to avoid having a given crime inflicted on them. 

The results are interesting:

| Percentile | Assault   | Robbery | Burglary |
|------------|-----------|---------|----------|
| 10th       | 1 day     | 1 hour  | 1 hour   |
| 25th       | 5 days    | 6 hours | 5 hours  |
| 50th       | 1 month   | 3 days  | 1 day    |
| 75th       | 6 months  | 2 weeks | 1 week   |
| 90th       | 3.5 years | 1 month | 2 months |

Even for ostensibly the most severe of the crimes participants were queried on, most would be willing to spend surprisingly little time in jail to avoid the crime. Many are detained for an assault for much longer than 1 month - without even considering their detention post-conviction. Yet 50% of people wouldn't be willing to spend more than a month in jail to avoid an assault. 

**Visualizing Relative Harm**

How much more, or less, harm does our justice system do than the people it detains? With the results from Stevenson and Mayson, we can visualize just that. Let's use robbery as a simple example - according to the Prison Policy Institute, 37,000 people are detained pretrial at any given time for robbery in the United States. Further, let us simplify as a start by only comparing the harm done to detainees against the harm done by their alleged crime.

To calculate the harm done by detention for robbery, we need only multiply the number of people detained by the length of their detention. To calculate the harm done *by* the robbery, we multiply the number of people who committed robberies by the relative harm of a robbery.

Imagine two axes, like the arms of a scale: one the harm allegedly done by the defendant in committing a crime (as represented by the 'relative harm' axis), and the other the harm done by the state to the defendant in detaining them pretrial (as represented by the 'detention' axis). In balancing the arms of this scale, we can imagine a third and final axis: the net harm done to those detained versus the harm done to victims of an alleged crime. What does that balance look like? 

{{<relharm3dseparate>}}

This balance can be viewed in two ways, in fact. One, like above, where we split the harm done to the detained and the harm done to the victims of an alleged crime. Where these two surfaces intersect, the harm done to the defendant is equal to the harm done to their alleged victim.

But another way of looking at it is as a single surface of net harm, where a negative Z-value indicates greater harm done to the detained than to the victims of crimes and a positive Z-value indicates greater harm done to the victims than to the detained.

{{<relharm3dnet>}}

In Stevenson and Mayson's paper, they analyze pretrial detention under a consequentialist framework. Under this framework, the benefit of detaining people pretrial is in the harm's prevented by that detention. Assuming that detaining a person pretrial only makes sense when the benefit of doing so outweighs the cost, we need only analyze the costs - the harm caused to detainees by detention - and the benefits - the hypothetical crimes prevented during detention. 

We calculate the harm to detainees the same way as before: the number of people detained multiplied by the length of their detention. Calculation of harm prevented is only modestly more complicated than our previous calculation. Here, we multiply the number of people who have committed a given crime by the 50th percentile relative harm value for that crime, then we multiply by our assumed probability of them recidivating. 

{{<harmbars>}}

Experiment with the lengths of detention and probability of recidivating. What does it take to achieve parity? How short of a detention, or how high of a likelihood of recidivation, is necessary to make the harm prevented equal the harm done? 



