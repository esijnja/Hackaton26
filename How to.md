How to

## Link USB devices to WSL

1. Open your PowerShell terminal.
2. type:
   ```
   usbipd list
   ```
3. note the `BUSID` of the USB devices `USB-Enhanced-SERIAL CH343`.
4. Type the following command to attach the USB device to WSL:
   ```
   usbipd attach --wsl --busid <BUSID>

1. Open your WSL terminal.
2. Type the following command to list all available USB devices:
   ```
   lsusb
   ```
1. Access the USB device by typing the following command:
   ```
   sudo chmod 666 /dev/ttyACM0
   ```
   ```
   sudo chmod 666 /dev/ttyACM0
   ```
   ``` 
   ls /dev/video*
   ```

   ```
   sudo chmod 666 /dev/video0
   ```
   ```
   sudo chmod 666 /dev/video1
   ```

1. Find which is which 

``` 
   lerobot-find-port
```

```
   lerobot-find-cameras 
```

2. write down leader and follower address

```
   leader: /dev/ttyACM0
   follower: /dev/ttyACM1
```

## Calibrate the leader and follower

Follower:

``` bash
lerobot-calibrate --robot.type=so101_follower --robot.port=/dev/ttyACM1 --robot.id=hackaton_follower_arm 
```

Leader:

> Doesn't work, `so101_leader` is not a valid robot type. Skip the step and go to Teleoperate the leader and follower.

``` bash
lerobot-calibrate --robot.type=so101_leader --robot.port=/dev/ttyACM1 --robot.id=hackaton_leader_arm 
```

## Teleoperate the leader and follower

``` bash
lerobot-teleoperate \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=hackaton_follower_arm \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM0 \
    --teleop.id=hackaton_leader_arm
```

Teleoperation with camera:

``` bash 
lerobot-teleoperate \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=hackaton_follower_arm \
    --robot.cameras="{ top: {type: opencv, index_or_path: 2, width: 640, height: 360, fps: 30}, wrist: {type: opencv, index_or_path: 0, width: 640, height: 480, fps: 30} }" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM0 \
    --teleop.id=hackaton_leader_arm \
    --display_data=false
```

## Recording

``` bash
lerobot-record \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=hackaton_follower_arm \
    --robot.cameras="{ top: { top: {type: opencv, index_or_path: 2, width: 640, height: 360, fps: 30}, wrist: {type: opencv, index_or_path: 0, width: 640, height: 480, fps: 30} }" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM0 \
    --teleop.id=hackaton_leader_arm \
    --dataset.num_episodes=15 \
    --dataset.single_task="put the orange brick in a yellow form" \
    --dataset.streaming_encoding=true \
    --dataset.root=${HOME}/hackaton26/recordings_data/so101_dataset_01 \
    --display_data=true
```

With only one camera :

``` bash
lerobot-record \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=hackaton_follower_arm \
    --robot.cameras="{ top: {type: opencv, index_or_path: 0, width: 640, height: 360, fps: 30} }" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM0 \
    --teleop.id=hackaton_leader_arm \
    --dataset.num_episodes=5 \
    --dataset.single_task="put the orange brick in a yellow form" \
    --dataset.streaming_encoding=true \
    --dataset.root=${HOME}/hackaton26/recordings_data/so101_dataset_01 \
    --display_data=true
```

With only one camera and repoid:

``` bash
lerobot-record \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=hackaton_follower_arm \
    --robot.cameras="{ top: {type: opencv, index_or_path: 0, width: 640, height: 360, fps: 30} }" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM0 \
    --teleop.id=hackaton_leader_arm \
    --dataset.repo_id=${HF_USER}/hackaton26_test \
    --dataset.num_episodes=5 \
    --dataset.single_task="put the orange brick in a yellow form" \
    --dataset.streaming_encoding=true \
    --display_data=false
```

``` bash
lerobot-record \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=hackaton_follower_arm \
    --robot.cameras="{ top: {type: opencv, index_or_path: 2, width: 640, height: 360, fps: 30}, wrist: {type: opencv, index_or_path: 0, width: 640, height: 480, fps: 30} }" \
    --teleop.type=so101_leader \
    --teleop.port=/dev/ttyACM0 \
    --teleop.id=hackaton_leader_arm \
    --dataset.repo_id=${HF_USER}/hackaton26_test \
    --dataset.num_episodes=15 \
    --dataset.single_task="put the orange brick in a yellow form" \
    --dataset.streaming_encoding=true \
    --display_data=false
```

## Training

copy data to BB laptop

``` bash
scp -r lerobot@192.168.68.75:/home/lerobot/.cache/huggingface/lerobot/esijnja/ ~/.cache/huggingface/lerobot/esijnja/
```

``` bash
lerobot-train \
    --dataset.repo_id=${HF_USER}/hackaton26_test_20261109_213455 \
    --policy.type=act \
    --output_dir=outputs/train/act_so101_test \
    --job_name=act_so101_test \
    --policy.device=cuda \
    --trackio.enable=true \
    --policy.repo_id=${HF_USER}/policy_test \
    --steps=20000
```

## Rollout

``` bash
lerobot-rollout \
    --strategy.type=base \
    --policy.path=${HF_USER}/policy_test \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=hackaton_follower_arm \
    --robot.cameras="{ top: {type: opencv, index_or_path: 2, width: 640, height: 360, fps: 30}, wrist: {type: opencv, index_or_path: 0, width: 640, height: 480, fps: 30} }" \
    --task="put the orange brick in a yellow form" \
    --duration=60
```

``` bash
lerobot-rollout \
    --strategy.type=base \
    --policy.path=${HF_USER}/Hackaton_test_5_20261110_205435 \
    --robot.type=so101_follower \
    --robot.port=/dev/ttyACM1 \
    --robot.id=hackaton_follower_arm \
    --robot.cameras="{ top: {type: opencv, index_or_path: 2, width: 640, height: 360, fps: 30}, wrist: {type: opencv, index_or_path: 0, width: 640, height: 480, fps: 30} }" \
    --task="put the orange brick in a yellow form" \
    --duration=60
```


