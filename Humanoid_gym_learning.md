# This note is to record my humanoid-gym learning process

## Process record
* **Prepare a GOOD urdf**
    * make sure the moving joints are set to revolute, and each of them has a limit to constrain their motion.You can get the limit with mujoco.
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






