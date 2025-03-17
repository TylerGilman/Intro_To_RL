## Multi armed bandit
k levers, at each time step t = 1, 2, 3,//// chose to pull one lever A_t from set of k possible actions

Recieve reward R_t after taking A_t
R_t assu,ed tp ne sampled IID from payout distrubtion of arm A_t (Indepndent and identically distributed)
- independence: independece of a pull of a particular machine is independent of previous pulls (or not pulls) on the same machine

## Bandit vs RL
RL agent reasons about what actions to take
- considers long term consequences (bandit is optmizing immediate return)
- Bandit has no state ("there is contextual bandit version...")
    - contextual bandit is RL w single timestep reward

*Working up to estimating Q\**

## Q-values
Learning: Estimate the Q-values (eexpected rewards)
    $$q_*(a)=E[R_t|A_t=a]$$
*The expected value of choosing action a*

Given experience pulling an arm, how to estimate?
(approximation) $Q_t(a) = \frac{\text{sum of rewards when a taken prior to t}}{\text{number of times a taken prior to t}}$
$$= \frac{\sum^{t-1}_{i=1}R_i * I_{A_{i=a}}}{\sum^{t-1}_{i=1}I_{A_{i=a}}}$$

**Textbook uses captical letters (Q) for estimaties, lowercase letters (q) for true quantaties**

$Q_t(a)$ is a sample average, converges to $q^*$ in the limit (general pattern with monte carlo algos)

Example: 4-armed bandit
Action 1 - Reward is always 8
$q_*(1)$ = 8
Action 2 - 88% change of 0, 12% chace of 100
$q_*(2)$ = .88 * 0 + .12 * 100
Action 3 - Randomly between -10 and 35, equiprobable
$q_*(3)$
Action 4 - a third 0, a third 20, and a third from {9, 9, 18}
$q_*(4)$

*What if rewards are unknown?*

**Suppose you are faced with 4 arms, now with unkown payout. Suppose you can pull 100 times in total. How to act to maximize total expected return?**
### Exploration vs Explotation
**Two natrual possibilities:
1. Greedy action selection: $A_t = argmax_aQ_t(a)$
    Exploitation
2. Exploration

## $\epsilon$ greedy
1. With probability 1-$\epsilon$ take greedy action (exploit)
2. With probability $\epsilon$ take action uniformly at random

**What are some advantages to this policy?**
Will be able to simply explore and learn estimates

**What are some disadvantages?**
Over the long term the q estimate should become good and there should be less Exploration


**What is probability of the greedy action?**
a = number of avaliable actions
$E + ((1-e)/a)$ i think?

We *never* want to stop exploring to find estimate limit

*Is this similar to simulated annealing*
- "Simulated is somewhat different and just optimizer, but prof says not good comp..."

*Dynamic Programming*
- Solves MDP (In 5 lectures)

## Simple bandit algo
Initialize, for a = 1 to k:
Q(a) <- 0 - estimate for q value 
N(a) <- 0 - Number of *visits* to action
Loop forever:
    ..$$N(A) \leftarrow N(A) + 1$$.
$$Q(A)) \leftarrow Q(A) + \frac{1}{N(A)}[R-Q(A)]$$
Incremental estimate update

$Q_n = \frac{R_1 + R_2 + ...+ R_{n-1}}{n-1}$
requires an unbounded amount of memory!
Actually, we can use our previous Q value 
...
$$Q_{n+1}=Q_n + \frac{1}{n}[R_n - Q_n]$$
Now we have Q-value estimation in incremental form!!
Instead of remembering all the rewards and taking average each time we take just new reward and update q
Full derivation...
![[Pasted image 20250110162016.png]]
NewEstimate $\leftarrow$ OldEstimate + StepSize[ Target - OldEstimate ]
To simplify n
The amount we adjust the estimate to a new target is step size (can be constant here $\frac{1}{n}$)
Estimator "follows the target"
$[Target - OldEstimate]$ = **Error**
## Example: 10-armed bandit (txtbook page 34)
Each individual action is a draw from gaussian (or other) distribution of $q_*$
variance

![[Pasted image 20250110163252.png]]
Showing average performance/return of bandit after certain steps of learning... Learning curve
*random agent would have 0 reward at step x*
**How is the greedy algorithm initial policy**?
- greedy policy still learns in initial timesteps
**Point of confusion** - distinction between $\epsilon$ = 0 and random choice agent
- $\epsilon$ = 0 case will still learn actions that yield rewards, but not will not progress from initial
- After a long time lower epsilon values will yield better rewards since they can exploit more, longer to converge since less exploration
*What type of $\epsilon$ should you chose depends on time horizon of application*

*What if the true action-value $q^*$ change over time?*
### **Non-stationary** problem
"World is dynamic and has ability to change"
Incremental Q-value estimation from before insufficient....
As time goes on subsequent rewards affect Q less... Bad for dynamic Q
"Sample average contains *antiquated info*"
$$Q_{n+1}=Q_n + \frac{1}{n}[R_n - Q_n]$$
instead...
$$Q_{n+1} = Q_n + \alpha[R_n-Q_n]$$
$$ = (1-\alpha)^nQ_1 + \sum^{n}_{i=1}\alpha(1-\alpha)^{n-i}R_i$$
Now step size in fixed and does not change over time...
(exponentiall recency-weighted average)
Reward given to recent reward is higher
$\alpha$ is the step size parameter
## Exponential recency-weighted average
opposite of previous derivation
(diminishing) bias towards $Q_1$
**Necessary for monte carlo convergence**
To ensure convergence with probability 100
$$\sum^\inf_{=1}\alpha_n(a)=\inf \text{and} \sum^\inf_{n=1} \alpha^2_n(a) < \inf$$
Laarge enough to overcome initial bias, and small enough to guarantee convergence
Does constant step-size $\alpha_n$(a) = $\alpha_{\text{constant}} satisfy this?
- constant step-size does not converge fully to true value...
- always will have noise in estimate
How about $\alpha(a)=\frac{1}{n}$ or $\alpha_n(a)=\frac{1}{n^2}$
$\alpha_n(a)=\frac{1}{n^2}$ converges

