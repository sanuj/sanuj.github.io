---
layout: post
title:  "Reinforcement Learning 1"
date:   2016-09-01 00:00:00 +0530
---

## Books

- An Introduction to Reinforcement Learning, Sutton and
Barto, 1998. http://webdocs.cs.ualberta.ca/∼sutton/book/the-book.html
- Algorithms for Reinforcement Learning, Szepesvari. http://www.ualberta.ca/∼szepesva/papers/RLAlgsInMDPs.pdf

## What is Reinforcement Learning?

Intersection of many fields of science that studies the science of decision making.

Reward system - dopamine, big discoveries in neuroscience.

- Reinforcement learning is not supervised learning. No one tells the optimal decision for a given situation. Just a reward is given.
- Feedback is delayed and not instantaneous.
- non iid data. Imagine an agent (robot) moving in an environment. The data points (visual inputs) are going to be very much correlated.
- agent's actions affect the subsequent data it receives. What robot sees depends on the path it takes.

## Rewards

- A reward is a number or a scalar feedback signal.
- Indicates how well agent is doing at step t.
- The agent's job is to maximize cumulative reward. RL is based on the reward hypothesis which states that all goals can be described by the maximization of expected cumulative reward.

Examples:

- flying stunt maneuvers in a helicopter. Feedback is given at the end of maneuver. +ve reward for following desired trajectory, -ve reward for crashing.
- manage an investment portfolio. trading agent making investment decisions based on data it receives. +ve reward for each $ in bank.
- control a power station. Make sequence of controls to increase efficiency of generation. +ve reward for producing power. −ve reward for exceeding safety thresholds.
- make a humanoid robot walk. +ve / -ve reward for forward motion / falling over.
- atari games. +ve / -ve reward for increasing / decreasing score.
- alpha go!

Videos of helicopter and atari games.

## Sequential Decision making
The goal is to select actions to maximize total future reward. These actions may have long term consequences and the reward may be delayed. It may be better to sacrifice immediate reward to gain more long-term reward.
Examples:

- A financial investment (may take months to mature).
- Refueling a helicopter (might prevent a crash in several hours).
- Blocking opponent moves (might help winning chances many moves from now).

## History and State
The history is the sequence of observations, actions, rewards upto time t. What happens next depends on the history. The agent selects actions and the environment gives observations and rewards. But the history is very long and we need something better to predict the action.

**State** is a summary of history that can be used to determine what happens next. It is simply a function of history.

- **Environment State** is its private representation of data that is used to pick next observation and reward. The environment state is not usually visible to the agent. E.g. a robot only sees the video stream available and not anything else happening elsewhere. Sometimes, the subjective knowledge of environment is good because the rest of the information may be redundant. E.g. a robot in India generally doesn't care what's happening in Australia.
- **Agent State** is the agent's internal representation of information required to pick next action. This is the information that RL algorithms use.
- **Information State** or **Markov state** is a theoretical concept which contains all useful Information from the history.
It means that future is independent of the past given the present i.e. the present state characterizes everything perfectly from the past. The state is a sufficient statistic of the future. E.g. for the helicopter example, the markov state will contain the information about its position, angular position, speed, angular speed, wind velocity. Non markov state is when we only know the position but not the velocity.
- The Environment state is Markov.
- The history is Markov.
- The way the state is represented affects what actions will be taken by the agent. Our job is to find the bes state representation.

## Fully Observable Environments
- observation = agent state = environment state.
- Formally this is a Markov Decision process (MDP).

## Partially Observable Environments
- Agent state != environment state. Agent indirectly observes environment. E.g. A robot with camera vision isn't told its absolute location. A trading agent only observes the current price. A poker playing agent only observes public cards.
- Formally, this is a partially observable Markov decision process (POMDP).
- Agent must construct its own state representation. One naive way would be to remember all of the history. Or we can build beliefs (probabilistic or bayesian approach) by remembering some probability distributions. It is not necessary to have probabilities to represent the state representation, we can have any type of numbers e.g. a recurrent neural network which uses a linear combination of current observation and previous state, wrapped by a non-linearity.

## Inside an RL agent

- An RL agent may include one or more of these components (not an exclusive list):
    - Policy: agent's behavior function -- how the agent picks its actions (by using the information in the current state). It is a map from state to action like a deterministic policy : a = pi(s) or a stochastic policy pi(a|s) = P[At=a|St=s]. Stochastic policy helps to make random exploratory decisions to see more of state space. We want to figure out the mapping which optimizes the reward.
    - Value function: how good is it to be in a particular state, how rewarding was a particular action. Estimating how well we are doing in a particular situation. It is a prediction of **future reward** which is used to evaluate goodness / badness of states.
    - Model: how the agent thinks the environment works. It is not the environment itself but an approximation of it. **Transitions** P predicts the next state (like dynamics). **Rewards** R predicts the next immediate reward. It is not necessary to build a model of the environment but can be done.

## Maze Example

- It's canonical RL example used for didactic purposes.

Example of deterministic policy function.

Example of value function.

Example of a model.

## Taxonomy of RL agents

- **Value based** -- if it stores the value function and no policy.
- **Policy based** --  if it stores the policy but no value function.
- **Actor critic** -- stores policy and value function.
- **Model free** -- we don't want to explicitly understand the environment but only use a policy or value function.
- **Model based** -- when we use a model to explain the environment along with policy and value function.

## Problems within RL

- Learning and Planning - two problems in decision
