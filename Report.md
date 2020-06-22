# Project 3: Collaboration and Competition
### Author: Angelo Antonio Manzatto 
------------------------------
### Introduction

In this environment, two agents control rackets to bounce a ball over a net. If an agent hits the ball over the net, it receives a reward of +0.1. If an agent lets a ball hit the ground or hits the ball out of bounds, it receives a reward of -0.01. Thus, the goal of each agent is to keep the ball in play.

### Space State
------------------------------------------------------

The state space, for each agent, has 24 dimensions containing 8 variables corresponding to the position and velocity of the ball and racket. Each agent receives its own, local observation. Two continuous actions are available, corresponding to movement toward (or away from) the net, and jumping.

### Action State
------------------------------------------------------

Two continuous actions are available, corresponding to movement toward (or away from) the net, ranging from -1 to 1

### Acceptance Criteria
------------------------------------------------------
The task is episodic, and in order to solve the environment, your agents must get an average score of +0.5 (over 100 consecutive episodes, after taking the maximum over both agents). Specifically,

* After each episode, we add up the rewards that each agent received (without discounting), to get a score for each agent. 
* This yields 2 (potentially different) scores. We then take the maximum of these 2 scores.
This yields a single score for each episode.

The environment is considered solved, when the average (over 100 episodes) of those scores is at least +0.5.

### Model 
------------------------------------------------------
Two Aritficial Neural Network models (Actor and Critic) were used fore each agent on this project contains just fully connected layers using an output with Tanh activation in order to constraint the arm movement between -1 and 1

Actor Model:

* FC Layer 1 (states, 256)
* FC Layer 2 (256, 256)
* FC Layer 3 (128, actions)
* Critic Model

Critic Model:

* FC Layer 1 (states, 256)
* Concatenate(states + actions)
* FC Layer 2 (states + actions, 128)
* FC Layer 3 (128, actions)

The number of states are 24 while there are 2 actions avaliable corresponding to the agent movement (forward, backward)

### Algorithm 
------------------------------------------------------
This project uses a **Multi AgentDeep Deterministic Policy Gradients (MADDPG) ** algorithm to train two agents to play tennis "cooperatively" against each other trying to maintain the ball all the time above the floor inside the tennis court limits.

MADDPG is the master algorithm that controls multiple DDPG agents that must cooperate with each other in order to solver the problem.  It uses a SHARED replay buffer to sample past experiences to remove the strong correlation between sequence of action that results in a deterministic state while learning to  help the agents on how they should react in different situations that are not frequent.

DDPG is an algorithm which concurrently learns q Q-funciona and a policy. It uses two models, one called Actor and the other called critic that directly maps states to actions intead of outputting the probability distribution across a discrete action space. 

Also it has two target networks that are time-delayed copies of their original networks that slowly track the learned networks in order to improve stability while learning.

The steps that the algorithm performs can be seeing as below:

![Plot](/images/ddpg-algorithm.png)


Each agent is trainned independntly using samples from this 

The parameter from the target network are updated using a process called "soft update" that uses part of the learned network in order to slowly update its own weights.

Since we are learning a continuous space instead of a discrete one the exploration is done by adding noise to the action itself using a method called Ornsterin-Uhlenbeck Process.

Some important notes:

- The algorith is VERY sensitive to hyper parameter tunning. I had to adjust the learning rate from the CRITIC network a little bit in order to leave the zero score. Another thing that greatly impact the agent performance while learning is the TAU hyperparameter responsable for the softupdate merging and the amount of noise applied while on the ACT step to performa exploration / exploitation.
- You have to be patient. I tried many approaches before the final one because I thought it was not learning at all while in fact it started reaching a score higher than zero after some epochs.

###  Hyperparameters
------------------------------------------------------
The parameters used during training were:

| Hyperparameter | Value     | Description |
| ----------- | ----------- | ----------- |
|BUFFER_SIZE | 1e5|replay buffer size|
|BATCH_SIZE | 256 |minibatch size|
|GAMMA | 0.99 |discount factor|
|TAU | 2e-2|for soft update of target parameters|
|LR_ACTOR| 1e-3| learning rate|
|LR_LR_CRITIC| 1e-3| learning rate|
|WEIGHT_DECAY| 0 |how the learning rate should decay over time|
|N_TIME_STEPS| 1 |every n time step do update|
|N_LEARN_UPDATES| 1 |number of learning updates|

### Evalutation
-----------------------------------------------------
The problem was solved after 670 episodes.

Below we can see  a plot of rewards per episode.

![Plot](/images/results.png)

### Ideas for the future
-----------------------------------------------------

- Use batch normalization to speed things a little bit.
- Implement the soccer challenge.
- Try solving the same problema using other Deep learning algorithms
- Can two robotic arms cooperate with each other ? I would like to test that on a ROS environment.

The ultimate challenge would be to build a C++ solution instead of a Python, but first I am buing a simple hpp file header that implements deep learning that can be found at: https://github.com/AngeloManzatto/ANN
