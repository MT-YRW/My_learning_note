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
    * i think there is something wrong with urdf file. i compare my urdf and XBOT, i find that only thigh, calf and foot has collision attribute. i modify this bug but the contact force between foot and ground still incorrect.
    * 
## Error record
* **Tensor error**
    * i mixed up about **"foot_name"** and **"knee_name"** in humanoid_config.py. I thought it means joint name but actually it means **link name**, which caused the code can not find foot and knee so the corresponding tensor is empty.
* **Reward always zero**
    * **DO NOT** set the **"only_positive_rewards"** to True! Humanoid gym is designed for XBOT robot, its initial parameter does not suitable for our robot. At the very begining the reward turns to be a negative number, if you set "only_positive_rewards" to True, the reward will be cut to zero.

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
    *






