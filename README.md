# Discovery
Resources for Data-driven grammatical hypothesis generation in concept learning project

For easy reference, we we provide additional details about the models we use in this project below. Specifically, we detail the Context-Free Grammar we use to model normative inference in compositional spaces, the Instance Driven Generation procedure we propose for modelling data-driven hypothesis generation and our approach to Scene Similarity Analysis.

### Pilot demo and data

[Task instructions and demo](https://www.bramleylab.ppls.ed.ac.uk/experiments/zendo/task.html)
<!-- (https://neilrbramley.com/experiments/zendo/main.html) -->

[Participant data](https://github.com/neilbramley/discovery/blob/master/results_processed.rdata)

[PCFG weights](https://github.com/neilbramley/discovery/blob/master/pcfg_weights.csv)

<!-- [Technical annex](https://github.com/neilbramley/discovery/blob/master/technical_annex.pdf) -->


## “Top-down” Context-Free Generation

One solution to the problem of learning within an infinite hypothesis space is to sample hypotheses by composing them stochastically from an underlying grammar of sufficient expressivity. In our pilot work, we modelled compositional sampling using a probabilistic context free grammar (or PCFG) Ginsburg (1966) that can produce any rule that can be expressed with first-order logic and lambda abstraction. Note that lambda abstraction provides a simple general formalism for binding entities to variables Church (1932). If a broad enough sample of hypotheses are generated in this way and scored based on their ability to capture available data, it is possible to approximate Bayesian inference Goodman, Tenenbaum, Feldman, and Griffiths (2008). In the current context, binding sets of objects to different variables means that, in the limit, PCFG samples will make all possible logical assertions about the features of, and relations between, subsets of objects within a scene that are necessary and sufficient for a scene to be “rule following”. For instance, grammatical statements include things like “blue objects may not be touching”, or “three objects must be facing the same way”, or “all objects must be green or horizontal but no more than one can be large”, and so on.

To draw a sample hypothesis using the PCFG in Table 1, one starts with a string containing a single non- terminal symbol (here, S). Non-terminal symbols in the string are then iteratively replaced using expansions drawn from the options in the requisite row of Table 1. Each expansion replaces non-terminal symbols with a mixture of other non-terminal symbols and terminal fragments of first order logic, until no non-terminal symbols remain. The expansions are so designed that the final string is guaranteed to be a valid grammatical expression. Figure 1a gives two example samples from the PCFG detailed in Table 1.1 Because some of the expansions involve branching (e.g., B → H(B, B)), the resultant string can become arbitrarily long and complex, involving multiple Boolean functions and complex relationships between bound variables.

Followin Piantadosi, Tenenbaum, and Goodman (2016), we assume the latent space of possible concepts in our task to be those expressible in first-order logic combined with lambda abstraction and full knowledge of the objects’ relevant features. However, our choice of expansion rules is one of many that can produce this class of expressions. Furthermore, the probabilities for each expansion can be set separately. This all means that the PCFG framework is a flexible way of encoding a learner’s inductive bias, or prior, over a compositional hypothesis space with different choices of expansion and different weights shaping the frequency with which a particular expression will be produced. Provided the expansions that involve branching do not occur with too high a probability, shorter expressions will naturally be favoured, organically capturing Ockham’s razor.

### “Bottom-up” Instance-Driven Generation

Our Instance-Driven Generation (IDG) proposal (Bramley, Rothe, Tenenbaum, Xu, & Gureckis, 2018) is related to the PCFG idea but with one major difference. Rather than generating guesses entirely context-free, before checking them against the data, we propose that people generate guesses inspired by an encountered positive example Minton et al. (1989). Concretely, we propose that learners start by observing the features of objects in a rule-following scene and use these to back out a true logical statement about the scene in a stochastic but truth preserving way. In this way the learner does not generate uniformly from all possible logical statements, but directly from the restricted space of statements true of the current observation. Figure 1b motivates this approach. Here a learner begins their hypothesis generation with an observation of a scene that follows the hidden rule. To generate hypotheses as candidates for the hidden rule, we assume the learner uses the following procedure:
1. Observe. With uniform probability, either:
(a) Sample a object from the observation, then sample one of its features — e.g., #2:2 “medium, size”
or {#3}: “red, colour”.
(b) Sample two objects uniformly without replacement from the observation, and samples any shared or
pairwise feature — e.g., {#1,#2}: “size”, or “contact”
2. Functionise. Bind a variable for each sampled object in Step 1 and sample a true (in)equality statement relating the variable(s) and feature:
(a) For a statement involving an unordered feature there is only one possibility — e.g, {#3}: “= (x1,red,color)”, or for {#1,#2}: “=(x1,x2,color)”
(b) For a single object and an ordered feature this could also be a nonstrict inequality (≥ or ≤). We assume a learner only samples an inequality if it expands the number of objects picked out from the scene relative to an equality — e.g., in Figure 1b, there is also a large object {#1} so either ≥(x1,medium,size) or =(x1,medium,size) might be selected.
(c) For two objects and an ordered feature, either strict or non-strict inequalities could be sampled if the objects differ on the sampled feature, equivalently either equality or non-strict inequality could be selected if the objects do not differ on that dimension — e.g., {#1,#2} >(x1,x2,size), or {#3,#4} ≥(x1,x2,size).
3. Extend. With probability 1 go to Step 4, otherwise sample uniformly one of the following expansions 2
and repeat. For statements with two bound variables Step 3 is performed for x1, then again for x2:
(a) Conjunction. A object is sampled from the subset picked out by the statement thus far and one of its features sampled — e.g., {#1} ∧(= (x1,green,color), ≥(x1,medium,size)). Again, inequalities are sampled only if they increase the true set size relative to equality — e.g., “∧(≤ (x1, 3, xposition),
≥ (x1, medium, size))”, which picks out more objects than “∧(= (x1, 3, xposition), ≥ (x1, medium, size))”.
(b) Disjunction. An additional feature-value pair is selected uniformly from either unselected values of the current feature, or from a different feature — e.g., ∨(= (x1 , color, red), = (x1 , color, blue)) or ∨(= (x1, color, blue), ≥ (x1, size, 2)). This step is skipped if the statement is already true of all the objects in the scene.
4. Quantify. Given the contained statement, select true quantifier(s):
(a) For statements involving a single bound variable (i.e., those inspired by a single object in Step 1) the
possible quantifiers simply depend on the number of the objects in the scene for which the statement holds. The quantifier is selected uniformly from the existential ∃( ) or numerical “exactly N=( ,J)”, “at least N≥( ,J)”, or “at most N≤( ,J)” and J is set to match number of objects for which the statement is true. If it it is true for all objects, the universal quantifier ∀( ) may also be selected.
(b) Statements involving two bound variables in lambda calculus have two nested quantifier statements each selected as in (a). The inner statement quantifying x2 is selected first based on truth value of the expression while taking x1 to refer to the object observed in ‘1.’. The truth of the selected inner quantified statement is then assessed for all objects to select the outer quantifier — e.g., {#3,#4} “∧(= (x2, green, color), ≤ (x1, x2, size))” might become “∀(λx1 : ∃(λx2 : ∧ (= (x2, green, color), ≤ (x1, x2, size)), X ), X )”. The inner quantifier ∃ is selected because three of the four objects are green {#1, #2, #4}, and the outer statement is selected because all objects are less than or equal in size to a green object.
One way to think of the IDG procedure is as an inversion of a PCFG. As illustrated by the blue text in the examples in Figure 1, while the PCFG starts at the outside and works in, the IDG starts from the central content and works outward out to a quantified statement, ensuring at each step that this final statement is true of the scene. Note also that the specific algorithm we provide here is only an illustrative example that we expect to refine on the basis of our experiments.

## Authors

* **Neil Bramley** -[website](https://bramleylab.ppls.ed.ac.uk)
* **Chris Lucas** -[website](https://homepages.inf.ed.ac.uk/clucas2/)
<!-- * **Anselm Rothe** - [website](http://anselmrothe.github.io/) -->
<!-- * **Josh Tenenbaum** - [website](http://web.mit.edu/cocosci/josh.html) -->
<!-- * **Fei Xu** - [website](http://https://psychology.berkeley.edu/people/fei-xu) -->
<!-- * **Todd Gureckis** - [website](http://psych.nyu.edu/gureckis/) -->

## Acknowledgments

* Thanks to the Moore-Sloane Data Science Environment at NYU [website](https://cds.nyu.edu/mooresloan/)
