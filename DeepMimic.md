
based on **DeepMimic** (Peng, Abbeel, Levine, van de Panne, SIGGRAPH 2018)
- https://dl.acm.org/doi/abs/10.1145/3197517.3201311

## Baseline

input:
- character model (is a physically described model meant by that?)
- set of kinematic reference motions (reference motions as joint angles/ end eff positions/ both? and velocities?)
- task by a reward function (is the distribution of rewards meant by reward function, as this "defines" the task? the sum of different reward terms should be the reward function)
synthesized controller to imitate reference motion (just a pd on reference motion?)
what exactly is the task specific goal? it is referenced as an input for the control policy $\pi(a_{t}|s_{t}, g_{t})$
the action is then computed to torques, in other terms it is mapped, right? only if output of policy != torques
Policy: NN
Training: PPO ([[Reinforcement Learning]])

Goal of policy is: reproduce desired motion in physically simulated environment, while also satisfying additional task objectives

## States and Actions

State: 
- relative position of every link (is a link a joint, or a body part that connects joints?)
	- their rotations in quaternions
	- linear and angular velocities
- root = pelvis, x-Axis = facing direction
- additionally a phase variable $\phi\in[0,1]$ denotes start to finish of motion
	- cyclic motions reset to 0
- policies with additional task objective have goal $g$, which is similar to the state

(Action) What is meant by: 
The action $a$ from the policy specifies target orientations for PD controllers at
each joint. 
- sounds like the policy makes input for PD controller (target position), which outputs $a$
- i thought the PD has as input current angles vs target angles and the action combines from PD output + policy output
Action:
- Policy in $30Hz$, target orientations spherical = axis-angle, revolute = scalar angles
- the use of PD controllers abstracts away low-level control (like local damping and local feedback) compared to torques they have improved performance and learning speed 

## Network

each policy $\pi$ is represented by neural network
- maps state $s$ and goal $g$ to distribution over action $\pi(a|s,g)$ 
- action distribution is modeled as Gaussian with mean $\mu(s)$ and fixed diagonal covariance matrix $\Sigma$ as a hyperparameter of the algorithm $$\pi(a|s)=N(\mu(s),\Sigma)$$
	- confusing to me: NN have output layer, this can be interpreted as n discrete actions, or as one continuous action (e.g. each output is a torque input for one motor). how can this be a distribution of actions, as they output exactly n outputs?
	- is it meant, that it only outputs the mean? 
	- why is mean only state dependent?
- ![[Pasted image 20260805141801.png|655]]
	- design of network: $s$ and $g$ are processed by two fully-connected layers (ReLU) with 1024 and 512 units and a linear output layer
	- value function as similar network (what is the value function?)
	- image also shows structure if augmented with heightmap H of surrounding terrain

## Reward

- reward $r_{t}$ at each step $t$ consists of two terms: match reference motion and satisfy additional task objectives: $$r_{t}=\omega^Ir^I_{t}+\omega^Gr^G_{t}$$
- $r^I_{t}$ and $r^G_{t}$ are the imitation (follow motion $\{\hat{q}_t\}$) and task objectives (fulfill task-specific objective), with $\omega^I$ and $\omega^G$ their weights
- then follow some explanation of different terms of the imitation objectives (like pose, velocity, end-effector and center-of-mass reward)

## Training

- is trained with [PPO](https://arxiv.org/pdf/1707.06347) using clipped surrogate objective???
- one network for policy $\pi_\theta(a|s,g)$ and one for value function $V_\psi(s,g)$ 
- ...

**Initial State Distribution**
- very important design choices:
	- initial state distribution $p(s_0)$ being spread over all frames
	- therefore not having fixed-length episodes
	- not! always same starting state, as agent has to gradually learn, not all at once
	- easy with reference motion: starting state is sampled from for initialization
		- called _reference state initialization_ (RSI)
	- very good for complex task: agents learns that motion has states with higher reward -> motivation (e.g. to learn a jump)
	- thus RSI can be interpreted as channel for information from reference motion

**Early Termination**
- cyclic skills can be infinite horizon MDP - what is MDP?
	- during training each episode is simulated for finite horizon
- episode terminates after fixed time, or when certain termination conditions are triggered
	- without early termination, data in early stages of training is dominated by samples of character struggling on ground 
		- network try's to adapt to those states

## Multi-Skill Integration

