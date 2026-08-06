
## Key Concepts

![[Pasted image 20260716115702.png]]

following explains terminology

### States and Observations

**State _s_** is complete description of the state of the world. 
**Observation _o_** is partial description of a state.

In deep RL, we almost always represent states and observations by a [real-valued vector, matrix, or higher-order tensor](https://en.wikipedia.org/wiki/Real_coordinate_space). For instance, a visual observation could be represented by the RGB matrix of its pixel values; the state of a robot might be represented by its joint angles and velocities.

Terminology: **fully observed**/ **partially observed**

### Action Spaces

Set of actions in given environment is **action space**.
Some envs have **discrete action space** with finite number of moves (like chess)
	others have **continuous action space** with real-valued vectors, like control of robot in physical world

### Policies

**Policy** is rule used by agent to decide what actions to take
**Policies** can be **deterministic**: $a_t=\phi(s_{t})$
	or **stochastic**: $a_{t}\sim\pi(\cdot|s_{t})$
"agent" can be called "policy"

Example deterministic: MLP
Examples stochastic: **categorical policies**/ **diagonal Gaussian policies**
cp in discrete action spaces, dgp in continuous action spaces
#### stochastic Policies
Two key computations are centrally important for using and training stochastic policies:

- sampling actions from the policy,
- and computing log likelihoods of particular actions,  $\log\pi_{\theta}(\cdot|s_{t})$

**categorical policies**:
- like a classifier over discrete actions: input is observation, hidden layer, output logits -> softmax
- then sampling of acions from likelihood
- loglikelihood for backpropagation, makes \* -> + -> easier derivation for gradients and 

**diagonal Gaussian Policies**
- special case of multivariate Gaussian distribution, with mean vector $\phi$ and the covariance matrix $\Sigma$ as a diagonal matrix, so it can be represented by a vector
- nn maps from observation to mean actions $\mu_{\theta}(s)$ and 
	- 1. vector of log standard deviations, $\log \sigma$, not a function of state, they are standalone parameters (e.g. PPO in [[Praxisbericht]]) 
	- 2. nn maps from states to log standard deviations $\log \sigma_{\theta}(s)$, may share some layers with mean network
	- after sampling of dimensions of action $a=\mu_{\theta}(s)+\sigma_{\theta}(s)\odot z$
	- and log likelihood with probalistic density 

### Trajectories

**Trajectory $\tau$** is sequence of states and actions in world
State transition from t to t+1, depend only on most recent action and have natural laws of env
	deterministic: $s_{t+1}=f(s_{t}, a_{t})$
	stochastic : $s_{t+1}\sim P(\cdot|s_{t}, a_{t})$

### Reward and Return

$r_{t}=R(s_{t},a_{t},s_{t+1})$

goal of agent is to maximize cumulative reward over trajectory. There are different cases 
**finite-horizon undiscounted return**: $$R(\tau)=\sum_{t=0}^{T}r_{t}$$**infinite-horizon discounted return**, $\gamma\in(0,1)$: $$R(\tau)=\sum_{t=0}^{\infty}\gamma^tr_{t}$$
### Other Notes:

- The key insight is that `reward` tells us how good our _immediate_ action was, but the agent needs to learn about _long-term_ consequences. Q-learning handles this by estimating the total future reward, not just the immediate reward.

# David Silver - RL Course

## Reward

Reward Hypothesis:
"All goals can be described by the maximization of expected cumulative reward."
- can only be at end (e.g. win/loose)

## Observability

Full observability:
$$O_{t}=S^a_{t}=S^e_{t}$$
- _Markov decision process_ = MDP

Partial observability:
agent state != environment state
- _partially observable Markov decision process_ = POMDP
- agent must construct its own state representation $S^a_t$ 
	- complete history: $S^a_t=H_t$
	- _Beliefs_ of environment state: $S^a_t=(\mathbb{P}[S^e_t=s^1],\ldots,\mathbb{P}[S^e_t=s^n])$ 
	- RNN

## Value Function

- prediction of future reward
- used to evaluate goodness/ badness of states 
- and select between actions $$v_{\pi}(s)=\mathbb{E}_{\pi}[R_{t}+\gamma R_{t+1}+\gamma^2R_{t+2}+\ldots|S_{t}=s]$$

## Model

- model predicts what environment will do next
- _Transitions_: $\mathcal{P}$ predicts the next state
- _Rewards_: $\mathcal{R}$ predicts the next (immediate) reward
$$\mathcal{P}^a_{ss'}=\mathbb{P}[S'=s'|S=s,A=a]$$
$$\mathcal{R}^a_{s}=\mathbb{E}[R|S=s,A=a]$$
- models are not necessary

## Categorizing RL agents

- value based
	- No Policy (implicit)
	- Value Function
- policy based
	- policy
	- no value function
- actor critic
	- policy
	- value function

- Model Free
	- Policy and/or Value Function
	- no model
- Model based
	- policy and/or value function
	- model

## Learning and Planning

- Reinforcement Learning:
	- environment is initially unknown
	- agent interacts -> improves policy
- Planning:
	- model of environment is known
	- agent performs computations with its model
		- agent improves policy

## Return

return $G_{t}$ is total discounted reward from time-step t
$$G_{t}=R_{t+1}+\gamma R_{t+2}+\ldots=\sum^\infty_{k=0}\gamma^kR_{t+k+1}$$

## Markov Reward Processes

Bellman Equation for MRPs: $$v(s)=\mathbb{E}[R_{t+1}+\gamma v(S_{t+1})|S_{t}=s]$$

## Markov Decision Processes

**Value Function**
 - *state-value function* $v_\pi(s)$ of MDP is expected return starting from state s, and then following policy $\pi$  $$v_{\pi}=\mathbb{E}_{\pi}[G_{t}|S_{t}=s]$$
 - *action-value function* $q_\pi(s,a)$ is expected return starting state s, taking action a, and then following policy $\pi$ 
 $$q_{\pi}(s,a)=\mathbb{E}_{\pi}[G_{t}|S_{t}=s,A_{t}=a]$$

### Sources

- https://spinningup.openai.com/en/latest/spinningup/rl_intro.html
- https://gymnasium.farama.org/introduction/train_agent/
- David Silver: https://www.youtube.com/watch?v=2pWv7GOvuf0