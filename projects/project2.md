---
layout: page
title: "Clash-Royale AI agent 🏰⚔️"
permalink: /projects/project2.html
---

## AI Agent Visualization 🎮🤖✨

## 🔬 Brief Project Flow 📝

Since we have no access to the internal control for Clash Royale 🏰, so that no direct information to extract state, action, and reward for traditional online training in Reinforcement Learning. We have to build the visual detection block 👀 to extract the necessary information for training data. 

1. **Dataset/Episode Collection 📸**
   - 1.1 Use [Scrcpy](https://github.com/Genymobile/scrcpy) and **FFmpeg** 🎬 to access the Android phone screen and extract the game episode frames (later add the link for the dataset collection). Notice we have to use the Expert datasets 🏆.
   
   - 1.2 Prepare troops segments 🤖🛡️ for generative arena images, which is the training dataset for visual detection model YOLOv8:
  
   ![Generative Arena View](generative_arena.png)
     
3. **Visual Detection/ Data Processing 🔍**
   - 2.1 **State builder** (torch_state_builder.py): Use trained YOLOv8 🐶 for arena information extraction to build state **s**, use ResNet classification 🖼️ to extract info for deployed cards and elixir 💧, use cnocr 🔤 to extract actual time ⏱️.
   
   - 2.2 **Action builder** (torch_action_builder.py): Use ResNet classification 🖼️ to visually detect card deployments 🃏.
   
   - 2.3 **Reward builder** (torch_reward_builder.py): Use cnocr 🔤 to detect queen 👑 and king 🏰 tower HP deduction 💔.
   
   - 2.4 Every frame would have the corresponding state (s) 🟢, action (a) ⚡, and reward (r) ⭐

4. **Policy Network Construction 🧠**
   
 ![Policy Network Preview](policy_model_en.png)

6. **AWS Sagemaker Training ☁️**

## Training Dataset Construction 🗂️

1. The keys of **state (s)** are [arena 🏟️, cards 🃏, elixir 💧]. The trained YOLO model gives inference on arena detection results and detected troops with keys [position 📍, class 🎯, belongings 🎒]. The ResNet model detects the cards table 🎴, showing only 4 cards at a time. cnocr 🔤 detects remaining elixir.

2. The keys of **action (a)** are [select 🖐️, pos 📍] representing selected cards and deployment positions in the arena. The arena is divided into 32x18 sub-blocks.

3. The key of **reward (r)** is the scalar reward 🏅. Positive reward if opposite tower HP is reduced 💥, negative otherwise 😢.

4. Dataloading process: each timestep t ⏱️ has (s, a, r) pairs. The sequential construction is necessary for transformer decoder model 🔄.
   - Sequential Dataloader: Each sequence is length n (ex. 30), with the first 29 input pairs predicting the 30th pair 🧩.

5. **Sparse Action Addressing ⚡**
   
 ![Frame Re-weighting](frame_weights.png)

The actual action frames get higher weights 🏋️‍♂️, and neighboring frames get decaying weights ⬇️.

## Model Structure Discussion 🏗️

## References 📚
