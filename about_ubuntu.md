# This note is to record my ubuntu using

## Tools
* **basic command**
    * To rename file
    ```shell
    mv old_filename new_filename
    ```
    * To show system process status
    "ps" is the command to show system process status.
    -a/-u/-x is different options.
    "grep" is a command which is used to find text match pattern.
    ```shell
    ps -aux | grep <pattern>
    ```
    * About "cat"
    ```shell
    cat your_file # check the file name without open it
    cat file1 > file2 # overwrite file2 (or create file2)
    cat file1 >> file2 # add file1 after file2
    ```
    * To show NVIDIA GPU real-time status
    ```shell
    nvtop
    ```
* **ssh**\
    If you want to use ssh to remote control another device, make sure the target device has already installed and started ssh server.
    * To install ssh
    ```shell
    sudo apt install openssh-server
    sudo apt install openssh-client
    ```
    * To start ssh
    ```shell
    sudo systemctl start ssh
    sudo systemctl status ssh # check ssh status
    ```
    * To check ip
    ```shell
    ifconfig
    ```
    * To connect with target device
    ```shell
    ssh target_device_name@target_device_ip
    ```
    * Login without password
    ```shell
    ssh-copy-id target_device_name@target_device_host
    ```
    * Logout with ctrl+d
