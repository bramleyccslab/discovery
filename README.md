# Discovery
Resources for Data-driven grammatical hypothesis generation in concept learning project

### Experiments

[Task instructions and demo](https://www.bramleylab.ppls.ed.ac.uk/experiments/zendo/task.html)
<!-- (https://neilrbramley.com/experiments/zendo/main.html) -->

[Participant data](https://github.com/neilbramley/discovery/blob/master/results_processed.rdata)

[PCFG weights](https://github.com/neilbramley/discovery/blob/master/pcfg_weights.csv)

<!-- [Technical annex](https://github.com/neilbramley/discovery/blob/master/technical_annex.pdf) -->


For reference, we we provide additional details about the models we use in this project below. Specifically, we detail the Context-Free Grammar we use to model normative inference in compositional spaces, the Instance Driven Generation procedure we propose for modelling data-driven hypothesis generation and our approach to Scene Similarity Analysis.

## “Top-down” Context-Free Generation

One solution to the problem of learning within an infinite hypothesis space is to sample hypotheses by composing them stochastically from an underlying grammar of sufficient expressivity. In our pilot work, we modelled compositional sampling using a probabilistic context free grammar (or PCFG) Ginsburg (1966) that can produce any rule that can be expressed with first-order logic and lambda abstraction. Note that lambda abstraction provides a simple general formalism for binding entities to variables Church (1932). If a broad enough sample of hypotheses are generated in this way and scored based on their ability to capture available data, it is possible to approximate Bayesian inference Goodman, Tenenbaum, Feldman, and Griffiths (2008). In the current context, binding sets of objects to different variables means that, in the limit, PCFG samples will make all possible logical assertions about the features of, and relations between, subsets of objects within a scene that are necessary and sufficient for a scene to be “rule following”. For instance, grammatical statements include things like “blue objects may not be touching”, or “three objects must be facing the same way”, or “all objects must be green or horizontal but no more than one can be large”, and so on.

To draw a sample hypothesis using the PCFG in Table 1, one starts with a string containing a single non- terminal symbol (here, S). Non-terminal symbols in the string are then iteratively replaced using expansions drawn from the options in the requisite row of Table 1. Each expansion replaces non-terminal symbols with a mixture of other non-terminal symbols and terminal fragments of first order logic, until no non-terminal symbols remain. The expansions are so designed that the final string is guaranteed to be a valid grammatical expression. Figure 1a gives two example samples from the PCFG detailed in Table 1.1 Because some of the expansions involve branching (e.g., B → H(B, B)), the resultant string can become arbitrarily long and complex, involving multiple Boolean functions and complex relationships between bound variables.

Followin Piantadosi, Tenenbaum, and Goodman (2016), we assume the latent space of possible concepts in our task to be those expressible in first-order logic combined with lambda abstraction and full knowledge of the objects’ relevant features. However, our choice of expansion rules is one of many that can produce this class of expressions. Furthermore, the probabilities for each expansion can be set separately. This all means that the PCFG framework is a flexible way of encoding a learner’s inductive bias, or prior, over a compositional hypothesis space with different choices of expansion and different weights shaping the frequency with which a particular expression will be produced. Provided the expansions that involve branching do not occur with too high a probability, shorter expressions will naturally be favoured, organically capturing Ockham’s razor.

### “Bottom-up” Instance-Driven Generation

Our Instance-Driven Generation (IDG) proposal (Bramley, Rothe, Tenenbaum, Xu, & Gureckis, 2018) is related to the PCFG idea but with one major difference. Rather than generating guesses entirely context-free, before checking them against the data, we propose that people generate guesses inspired by an encountered positive example Minton et al. (1989). Concretely, we propose that learners start by observing the features of objects in a rule-following scene and use these to back out a true logical statement about the scene in a stochastic but truth preserving way. In this way the learner does not generate uniformly from all possible logical statements, but directly from the restricted space of statements true of the current observation. Figure 1b motivates this approach. Here a learner begins their hypothesis generation with an observation of a scene that follows the hidden rule. To generate hypotheses as candidates for the hidden rule, we assume the learner uses the following procedure:

## Authors

* **Neil Bramley** -[website](https://bramleylab.ppls.ed.ac.uk)
* **Chris Lucas** -[website](https://homepages.inf.ed.ac.uk/clucas2/)
<!-- * **Anselm Rothe** - [website](http://anselmrothe.github.io/) -->
<!-- * **Josh Tenenbaum** - [website](http://web.mit.edu/cocosci/josh.html) -->
<!-- * **Fei Xu** - [website](http://https://psychology.berkeley.edu/people/fei-xu) -->
<!-- * **Todd Gureckis** - [website](http://psych.nyu.edu/gureckis/) -->

## Acknowledgments

* Thanks to the Moore-Sloane Data Science Environment at NYU [website](https://cds.nyu.edu/mooresloan/)
