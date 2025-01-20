# This note is to record my humanoid-gym learning process
## 2025.1.14
i successfully run the code to train BHR-FC-2\
（but i got a very bad result）
### Process record
* **Prepare a GOOD urdf**
    * make sure the moving joints are set to **revolute**, and each of them has a **limit** to constrain their motion. You can get the limit by mujoco.
* **Modify the humanoid_config.py**
    * Asset
        ```shell
        file = '{LEGGED_GYM_ROOT_DIR}/resources/robots/BHR-FC-2/urdf/BHR-FC-2.urdf'
        name = "BHR-FC-2"
        foot_name = "foot"   # Sub-link name of the ankle
        knee_name = "calf"   # Sub-link name of knee
        terminate_after_contacts_on = ['torso']   # base link name 
        penalize_contacts_on = ["torso"]   
        ```
    * Init_state
        ```shell
        default_joint_angles = {  # = target angles [rad] when action = 0.0
            'lhipYaw_joint': 0.,
            'lhipRoll_joint': 0.,
            'lhipPitch_joint': 0.52,
            'lknee_joint': -1.17,
            'lankle1_joint': 0.8,
            'lankle2_joint': 0.,
            'rhipYaw_joint': 0.,
            'rhipRoll_joint': 0.,
            'rhipPitch_joint': -0.52,
            'rknee_joint': 1.17,
            'rankle1_joint': -0.8,
            'rankle2_joint': 0.,
        }
        ```
    * Control
        ```shell
        stiffness = {'hipRoll': 200.0, 'hipPitch': 350.0, 'hipYaw': 200.0,
                     'knee': 350.0, 'ankle': 15}
        damping = {'hipRoll': 10, 'hipPitch': 10, 'hipYaw':
                   10, 'knee': 10, 'ankle': 10}
        ```    
*  **Train**
    * Command line
        ```shell
        python train.py --task=humanoid_ppo --run_name your_name --num_envs 4096
        ```
## Hyperparameter tuning
* **About rewards**
    * i notice that rew_feet_contact_forces has been very low. So i try to modify the max_contact_force. Because the initial pose is a little bit high which may cause impact when landing, resulting in a large instantanous contect force.
    * i can't understand, how to check my contact force?? i need to know it.
    * The contact force is wrong. Why it is 4000+? Does isaacgym get the right link?
    * i think there is something wrong with urdf file. i compared my urdf and XBOT and found that in XBOT's urdf, only thigh, calf and foot has collision attribute. i modify this bug but the contact force between foot and ground still incorrect.
    * i closed all collision attribution except foot and torso, the contact force become a normal value.
    * Why the robot can walk normally but some of rewards are still negative?
## Error record
* **Tensor error**
    * i mixed up about **"foot_name"** and **"knee_name"** in humanoid_config.py. I thought it means joint name but actually it means **link name**, which caused the code can not find foot and knee so the corresponding tensor is empty.
* **Reward always zero**
    * **DO NOT** set the **"only_positive_rewards"** to True! Humanoid gym is designed for XBOT robot, its initial parameter does not suitable for our robot. At the very begining the reward turns to be a negative number, if you set "only_positive_rewards" to True, the reward will be cut to zero.
* **Device error**
    * i used server which has six GPU to train my policy, but i want to performing this policy on my computer, which only has one GPU. When i run play command it caused an error:**"Attempting to deserialize object on CUDA device 3 but torch.cuda.device_count() is 1."**. Why? Does the log file record the device information?
    * When the policy was saved by training device, each tensor will **save information about the training device**, in order to make the model transfer smoother and reduce the issues caused by hardware differences between training and inference. So when i change device to perform my policy, i need to use **map_location** to specify the device for loading the policy.
    ```shell
    loaded_dict = torch.load(path,map_location=lambda storage, loc: storage.cuda(0))
    ```
    where "loc" is the original location of the tensors. You can also implement a more complex mapping, such as:
    ```shell
    loaded_dict = torch.load(path, map_location=lambda storage, loc: storage.cuda(0) if 'cuda' in loc else storage.cpu())
    ```
    this means if loc include "cuda" string, then put tensors on "cuda:0". If loc is "cpu", then put tensors on CPU. 

## Daily log
* **2025.1.14**
    * i successfully trained BHR-FC-2 but get a very bad result. The final reward is -4.
* **2025.1.15**
    * Still a bad result :(
* **2025.1.17**
    * i added a new reward to encourage alive. It does extended episode length, but it still can't take a step.
    * Also, the way this code calls the reward function really surprised me. All i need to do is add a function under humanoid_env.py and add a scale under humanoid_config.py. Then the code will base on the scale's name to serch reward function and perform the calculation.
    * Because of my alive reward, this time i got 15.36 as the final reward.
* **2025.1.18**
    * i closed the collision attribution of links except foot and torso. Then the contact force become a normal value. 
    * The final reward is 338.3 and the episode length is 2335.01. It can walk normally now but some of rewards are still negative.
* **2025.1.20**
    * i found that policy 0118 makes my robot walk with a large knee angle. I guess it's because my target height was set to low. So I change it to 0.95 meters.





