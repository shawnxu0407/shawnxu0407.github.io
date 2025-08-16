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
 ![Policy Network Preview](policy_model_en.png)


5. **AWS Sagemaker Training**


## Training Dataset Construction

1. The keys of **state (s)** is [arena, cards, elixir] where those are all the useful visual information from the images to form the states. We call the trained YOLO model to give inference on   arena detection results including original image information and the detected troops with keys [position, class, belongings]. We call the trained Resnet model to detect the cards table as only 4 cards showing up in the cards table throught the total 8 cards. We use cnocr to detect the text for the number of elixir remaining for the current frame.


2. The keys of **action (a)** is [select, pos] which are the selected card and deployment positions in the current arena. We also divide the whole arena into 32x18 sub-blocks so that the deployment positions (x,y) are in [32,18].


3. The key of **reward (r)** is just the scalar for rewards received for the curret frame. We use cnocr to detect the relative HP detection for each queen's and king's tower. If we deduct the opposite tower's HP, we have positive reward, otherwise, we have the negative rewards.


4. Finally, the dataloading process will be introduced. Recall we have (s, a, r) pair for **each timestep t**, the sequential construction of pair is necessary for transformer decoder model.
   - Sequential Dataloader: Each data point is a sequence of length n (ex. 30 here), We have proceeding 29 pairs inputs of $(s_i, a_i, r_i)$ with the target pair $(s_{30}, a_{30}, r_{30})$. Briefly speaking, we use the model to address the first 29 pairs of input and output the predicted pair $(\hat{s}_{30}, \hat{a}_{30}, \hat{r}_{30})$ and minimize the distance between target and prediced pair.


5. **Sparse Action Addressing:**
   ![Frame Re-weighting](frame_weights.png)
Depending on our dataset, the number of action frames compared to the number of total frames is only $2.1%$. We should prepare the dataset so that the model can learn the sequential reasoning from the dataset having more action frames. The reweighting process is introduced that the actual **action frames** has more weights to be chosen than the **non-action frames**. Also, the adjacent frames besides the action frames would also have large weights to be chosen in the decaying way.


## Model Structure Discussion


## Reference
