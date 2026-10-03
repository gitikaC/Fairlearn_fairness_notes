# Fairness Assessment in Machine Learning
## What are we actually trying to find?
When evaluating fairness, I am not simply asking:
"Is my model biased?"

I'm trying to answer:
`Could the decisions made by this model disproportionately disadvantage certain groups?"

The important part is the impact of the model's decisions.

For my loan project, the decision is loan approval, so the possible harm is that some people or 
groups could have a harder time getting approved or could experience more errors form the model.

----------------------------------------------------------------------------------------
## 1) Start with the decision + possible harm
Before choosing any fairness metric, I need to understand:

What is the model deciding?
My model -> credit/loan decision

So this is an allocation decision because the system is helping decide who gets access to something (credit).

Possible problems could be:

- One group gets approved less often
- One group gets denied even when they should have been approved
- One group has more false positives
- One group has more false negatives


  So basically:

  First understand what could go wrong -> then decide how to measure it.

  -------------------------------------------------------------------------------------
 ## 2) Look at different groups

  Next, I need to decide which groups I want to compare.

  For my dataset, I might look at things like:

  - Sex
  - Foreign worker
  - Other relevant demographic/proxy variables

  But I shouldn't just say:
  "This is the sensitive variable, therefore the model is unfair."

  No.
  I'm using these groups to investigate whether the model behaves differently across them.

  --------------------------------------------------------------------------------

## 3) Overall metrics are not enough

  I already have my normal ML metrics:

  - Accuracy
  - Precision
  - Recall
  - F1
 
  There tell me how the model performs overall.

  But here's the problem.

  Imagine:

  Overall recall = 0.80

  That sounds fine, but maybe:

  Group A recall = 0.90
  Group B recall = 0.65

  Now I know the overall number was hiding something.
  So I need to calculate my normal metrics separately for each group.

  This is called **disaggregated evaluation**.

  Basically:
  `Instead of only asking "How good is my model?", I ask "How good is my model for each group?"`

  ----------------------------------------------------------------------------

## 4) This depends on the type of ML problem

  The general idea of fairness can apply to different types of ML, but the fairness metrics I use depend on what the model is doing.

  I can't use the exact same fairness metrics for classification, regression, unsupervised learning and reinforcement learning.

**Classification**:
  This is what I'm doing in my project.

  My model produces a decision such as:
  Approve/deny

  So I can look at:

  - Approval/selection rate
  - TPR/recall
  - FPR
  - Precision
  - False negatives
  - False positives
 
  And fairness definitions such as:
  - Demographic parity
  - Equal opportunity
  - Equalized odds
 
  make sense here because they are based on classification outcomes and errors.

**Regression**
  Regression predicts a continuous value instead of a class.

  For example:
  Predict income = $62000

  So TPR/FPR don't directly apply.

  I could instead compare things like:

  - MAE by group
  - RMSE by group
  - Prediction error by group
  - Underprediction/overprediction by group

  For example:
  Overall MAE = $5,000
  Group A MAE = $3,800
  Group B MAE = $7,200

  The question becomes whether the model's prediction errors differ across groups.

**Unsupervised learning**
  There may not even be a target variable.
  For example, If I'm clustering customers, I might investigate:

  - Representation of groups within clusters
  - Whether groups are disproportionately placed into certain clusters
  - Whether clustering quality differs across groups
  - Whether the clusters are later used for decisions that could cause harm
 
  So classification fairness metrics don't automatically apply.

**Reinforcement learning**
  The model is making decisions repeatedly over time:

  State -> Action -> Reward -> New state -> Action....

  So fairness might involve asking:

  - Do different groups receive different actions?
  - Do they receive different cumulative rewards?
  - Does one group experience more harmful outcomes?
  - Does the learned policy behave differently across groups?
 
  Again, the fairness measure has to match the actual decision and potential harm.

**Main Idea**
Fairness is not one universal checklist of metric. The metric needs to match the model type, the decision being made, and the potential harm.

For my project, I'm dealing with classification, so I'm using classification-specific fairness concepts.

------------------------------------------------------------------------------------------------------

## 5) Then look for disparities

Once I calculate the metrics for each group, I can compare them.

For example:

Metric           Group A         Group B
Approval rate    70%              45%
Recall/TPR       90%              65%   
FPR              10%              22%

Now I can see that the groups have different outcomes.

This is disparity.

But:
Disparity does not automatically mean that the model is unfair.

It means: 
Something is different between these groups, so I need to investigate why and whether it represents a meaningful harm.

That's an important distinction.

--------------------------------------------------------------------------------

## 6) Fairness metrics
This is where the actual fairness definition come in.

There isn't one single mathematical definition of fairness.

Different metrics basically ask different questions.

**Demographic / Statistical Parity**

Question: 

Are groups being approved/selected at similar rates?

For my project:

Are different groups getting approved for loans at similar rates?

**Equal Opportunity**
Question:
Among people who actually belong to the positive/qualified class, are groups being correctly identified at similar rates?

This looks at:
TPR/Recall

So I'm basically asking:

Are qualified people from different groups being approved at similar rates?

**Equalized Odds**

This goes a step furthe

Question:
Are both TPR and FPR similar between groups? 

So it looks at both:

- True positive rate
- False positive rate

----------------------------------------------------------------------------

## 7) Why do we have different fairness definitions?

Because "fair" can mean different things depending on the situation.

For example:

Demographic parity:
"Groups should have similar approval rates."

Equal parity:
"Groups should have similar approval rates."

Equal opportunity:
"Qualified people should have similar chances of being approved."

Equalized odds:
"Both types of prediction errors should be similar between groups."

These are not the same thing.

A model could look okay under one definition and show a disparity under another.

So I shouldn't just calculate 10 fairness metrics and pick whichever one makes my model look good. 

I need to ask:
Which definition actually makes sense for the harm I'm investigating?

----------------------------------------------------------------------------------------

## 8) Fairness metrics + normal ML metrics work together

I original thought fairness analysis might replace the normal model evaluation. 
It doesn't 
I need both.

Normal ML metrics
Tell me:
How well is the model predicting?

Group-level metrics
Tell me:
Does performance change across groups?

Fairness metrics
Tell me: 
How does that difference relate to a particular definition of fairness?

So the analysis is more like:

Overall performance -> Performance by group -> Identify disparities -> Apply relevant fairness definitions -> Interpret what those disparities actually mean

-------------------------------------------------------------------------------------

## 9) The part I need to be careful about

I shouldn't immediately write:

"The model is biased"

just because I see:

Group A approval: 70%
Group B approval: 45%

What I actually know is:
"There is a difference in approval rates between the groups."

Then I need to investigate things like:

- Are the groups very different in size?
- Are their actual credit-risk rates different?
- Are there different error rates?
- Are some features acting as proxiees?
- Is the difference caused by the model or already present in the data?
- Is the different large enough to matter?
- Does it represent a meaningful harm in this particular decision?

So fairness analysis is more about investigating and understanding disparities than simply giving the model a label.

--------------------------------------------------------------------

## My Fairness Workflow

For my project, the workflow will be:

**1) Understand the decision**
-> Automated loan approval

**2) Identify potential harm**
-> Who could be disadvantaged by the decision?

**3) Identify groups**
-> Sex, foreign worker, etc

**4)Check groups sizes + actual outcomes**
-> Don't blindly compare tiny groups.

**5) Look at overall mode performance**
-> Accuracy, precision, recall, F1

**6) Calculate the same metrics by group**
-> See whether the model behaves differently

**7) Identify disparities**
-> Where are the biggest differences?

**8) Use relevant fairness metrics**
-> Demographic parity, equal opportunity, equalized odds etc.

**9) Interpret**
-> What could these differences actually mean for people affected by the loan decision?

**10) Later connect this to my project's bigger question**
-> Profit vs Fairness

---------------------------------------------------------------------------------------
**One sentence I need to remember**
Fairness assessment isn't just about finding a fairness score. It's about understanding show is affected
by an automated decision, what could go wrong, whether different groups experience different outcomes or errors 
and whether those differences represent a meaningful concern in context.

