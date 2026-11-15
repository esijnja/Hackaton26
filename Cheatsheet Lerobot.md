# Cheatsheet Lerobot

## Download UltraVNC

```
https://uvnc.com/downloads/ultravnc.html
```

## Connect to the robot using UltraVNC

- Open UltraVNC Viewer.
- Enter:

```text
lerobot.local
```

- Click "Connect".
- username:

```text
lerobot
```

- password:

```text
L3r0b0t
```

## Start lelab

Open a terminal and type:

```text
lelab
```

The training of the arm has four stages:

- Teleoperation
- Creating a dataset
- Training
- running the trained model

### Teleoperation

Start with teleoperate:

- click the "Teleoperation" button.
- check if the follower (purple) is following the leader (black).

Note: The top camera has a resolution of 640X360 and the wrist camera has a resolution of 640x480.

### Creating a dataset

Start by clicking the "Select or create dataset" button.
Click "Create new dataset".
Enter a name for the new dataset and description.
Enter the number of episodes for the dataset. (e.g. 30 or more) The number of episodes determines how many times the robot will perform the task to collect data.
Click "Start recording" to begin collecting data for each episode.
During an episode, perform the task as the robot records the data, and when the task is complete you can proceed to the next episode. At the beginning of each episode, make sure the robot is in the correct starting position.

### Training

After collecting the dataset, click the "Train model" button.
Select the dataset you created.
Select an instance of the A10G.
Click "Start training" to begin the training process.

### Running the trained model

The progress of the trained model can be seen in the interface.
