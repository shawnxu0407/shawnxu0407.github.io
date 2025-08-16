---
layout: page
title: "Clash-Royale AI agent"
permalink: /projects/project2.html
---


## AI Agent Visualization 🎮🤖




## 🔬 Brief Project Flow

Since we have no access to the internal control for Clash Royale so that no direct information to extract state, action and reward for tradional online training in Reinforcement learning. We have to build the visual detection block to extract the necessary information for training data. 


1. **Dataset/Episode Collection**
   - 1.1 Use [Scrcpy](https://github.com/Genymobile/scrcpy) and **FFmpeg** to access the Android phone screen and extract the game episode frames (later add the link for the dataset collection). Notice we have to use the Expert datasets.
  
   
   - 1.2 Prepare troops segments 🤖 for generative arena images which is the training dataset for visual detection model YOLOv8.

2. **Visual Detection/ Data Processing**
   - 2.1 State builder (katacr/policy/offline/perceptron/torch_state_builder.py): Use trained YOLOv8 for arena information extraction to build state **s**, use resnet classification to extract infomation for deploy cards and elixir, use cnocr to extract actual time.
  
   
   - 2.2 Action builder (katacr/policy/offline/perceptron/torch_action_builder.py): Use resnet classification to visually detect the cards deployment.
  
   
   - 2.3 Reward builder (katacr/policy/offline/perceptron/torch_reward_builder.py): Use cnocr to detect the queen's and king's tower HP deduction.
  
   
   - 2.4 Every frame would have the corresponding state (s), action (a) and reward (r)

3. **Policy Network Construction**
   -3.1 ![[Policy Network Preview](policy_model_en.png)]


## Training Dataset Construction



## Model Structure Discussion


## Reference
