### English | [中文](README(中文).md)

# FW-max-ros

# 一、pre-positioning
## 1. Environment confirmation:
####      1.1. System:
            Ubuntu 16.04 / Ubuntu 18.04
####      1.2. ROS version:
            Kinetic / Melodic

## 2.Computer and Industrial PC Boot Configuration
####      2.1.1. rc.local File Verification and Setup
            Check if the rc.local file exists in the /etc/ directory:
####      2.1.2. If not present, execute:
                 sudo cp rc.local /etc  
####      2.1.3. If present, insert the following content above the exit 0 line in the rc.local file, then save:
                 sleep 2  
                 sudo ip link set can0 type can bitrate 500000  
                 sudo ip link set can0 up        
####      2.2. Pre-Boot CAN Card Connection Verification​
　　Ensure the CAN card is physically connected to the USB port of the industrial PC prior to system startup or reboot.      
####      2.3. Configuration Success Indication​
　　A successful setup is confirmed when both the RX (Receive) and TX (Transmit) indicators illuminate, validating proper communication establishment.            

### 3.The subsequent section will address the process of establishing a connection between the CAN card and the system.
####      3.1. Chassis CAN card connection:
    Specifically, Chassis CAN-H is to be connected to CANH of the CAN card connector plug, while Chassis CAN-L is to be connected to CANL of the CAN card connector plug.
    The following diagram illustrates the recommended configuration:
            
![](https://github.com/kefangkele/FW-max-ros/blob/main/images/CAN_Connection.png?raw=true)

####      3.2. Computer and robot communication connection:
    Subsequent to the completion of the aforementioned connection, the USB end of the CAN card cable should be inserted directly into the computer's USB port, thereby establishing a connection between the CAN card and the computer. Upon completion of the connection, the PWR light will illuminate.
####      3.3. Methodology for checking the success of USB connection:
    To initiate this process, one must access the terminal and enter the command: 
            lsusb
    If the output information displays content resembling that depicted in the following image, it can be deduced that CAN can be utilized without issue.

![](https://github.com/kefangkele/FW-max-ros/blob/main/images/terminal_state.png?raw=true)

####      3.4. Communication initiation:
    To initiate this process, it is necessary to open a new terminal and enter the following commands: 
            sudo ip link set can0 type can bitrate 500000 & sudo ip link set can0 up
    If the chassis is in the power-up state, the blue RX light will blink, and the red TX light will be on at the same time.The blinking of the RX light indicates that the Can0 card receives data, and the blinking of the TX light indicates that it sends data.

## 4. Communication Data Viewing
####      4.1 To install the can-utils tool, the following command should be executed:
            sudo apt-get install can-utils
####      4.2 View data command:
            candump can0
####      4.3. Terminal print the information normally:

![](https://github.com/kefangkele/FW-max-ros/blob/main/images/candump_print.png?raw=true)

## 5. ROS program operation
####      5.1. File structure:

![](https://github.com/kefangkele/FW-max-ros/blob/main/images/doc_tree.png?raw=true)

####      5.2.Compile workspace:  
    Open the terminal in your workspace/enter your workspace in the terminal, let the terminal be in the workspace level, then execute the command: 
            catkin build
            
####      5.3.Setting temporary environment variables:
    Execute the command in the terminal where you want to run the program:  
            source ~/your workspace (example: 5.1-ros2_ws)/devel/setup.bash  
    Or, to keep the terminal where you want to run the program at the workspace level, execute the command:    
            source devel/setup.bash  

![](https://github.com/kefangkele/FW-max-ros/blob/main/images/source.png?raw=true)

####      5.4. Run:  
    Execute the command: roslaunch yhs_can_control yhs_can_control.launch in the terminal where you have just entered the temporary environment variable.  
####      5.5. The terminal prints out a successful run message:  
![](https://github.com/kefangkele/FW-max-ros/blob/main/images/node_print.png?raw=true)

## 6. Motion Test:
####      6.1. Before testing, it is recommended to lift the chassis or send a minimal speed command to the chassis (e.g., 0.03).
####      6.2. Open a new terminal, navigate to your workspace directory (example: 5.1-ros2_ws), and execute:
            source devel/setup.bash
            OR (without entering the workspace directory):
            source ~/your workspace(example: 5.1-ros2_ws)/devel/setup.bash

####      6.3. Monitor topic
            rostopic echo /ctrl_fb

####      6.4. After executing step 6.3, if you see continuously updating feedback data in the terminal, the ROS driver package is functioning properly.
####      6.5. Send motion commands to control the chassis:
            6.5.1. Open a new terminal, navigate to your workspace directory, and set temporary environment variables:
                        source devel/setup.bash
                   OR (without entering the workspace directory):
                        source ~/your workspace(example: 5.1-ros2_ws)/devel/setup.bash
            6.5.2. Prepare the command (do not execute yet). Enter in the terminal:
                        rostopic pub -r 100 /ctrl_cmd
            6.5.3. Press the Tab key to autocomplete the command. After autocompletion, input gear, speed, and steering angle (note: angle units are in degrees, not radians). Once entered, press Enter. Switch the remote controller to auto mode — the red and blue LEDs on the CAN card will blink, and the chassis will start moving.      
      
