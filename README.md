# Some Explanation

See the README file itself for Radford Neal's explanation.

In `ex-mixdft` you find the scripts to run the algorithms (`ex` stands for experiment) in the "Markov chain sampling methods for Dirichlet process mixture models" report.

Make sure you first add `bin` to your `PATH`:

    export PATH=$PATH:./bin

Subsequently running `./scmds-met` creates a very, very large binary (so, in the script below we only run for 500 steps for now):

The (adjusted) content of this specific script:

	 # Test of Algorithm 5 with R=4 on the simple data set.
	 mix-spec slog-met 0 1 / x1 1 
	 model-spec slog-met real 0.1
	 data-spec slog-met 0 1 / sdata .

	 mc-spec slog-met repeat 100 met-indicators 5 gibbs-params 
	 mix-mc slog-met 1
	 mc-spec slog-met met-indicators 4 gibbs-params
	 mix-mc slog-met 500

    mix-display slog-met

The abbreviation `met` stands for Metropolis-Hastings. The variable `met-indicators` is variable `R` in the algorithm (the  number of iterations of the MH step).

It is possible to plot something with

    sudo apt-get install xplot
    mix-plt t C1 slog-met | xplot

The mixture model inference is implemented in `/mix`.

The `met-indicators` function can be found in [mix-mc.c](mix/mix-mc.c).

We are considering real values, so `model->type='R'`, see [model.h](util/model.h). Basically for MH, the only other option is binary. The log probably of an observation (called a "case") given a component is given by `case_prob`. The term `offsets` is used for the means of the components. The current number of selected groups is a global variable `N_active`.

You see that a new component is picked through:

    offsets[N_active*N_targets+t] = hyp->mean[t] + hyp->SD[t]*rand_gaussian();

And likewise the covariance matrix `noise_SD` is generated through a call to `prior_pick_sigma`.

Like you see per "observation" (case) `i` there are several "targets" `t`. So, for observation `i=2` you need to skip `N_targets` to reach the set of target values for `i=2`.

The call to `mix_sort` only removes unused "components" (that became empty because there are no observations related to it anymore).

Radford makes use of log probability, so the acceptance is formulated as `dp < 0`, where `dp = lp - np`, with `lp` the old likelihood, and `np` the likelihood of the proposal distribution.

I can't find where the usual updates to the component variables occurs...

You see reference to `it->` through the code. This is just a bookkeeping device for printing stats afterwards.
