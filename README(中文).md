### [English](README.md) | 中文

# FW-max-ros

## 1.环境确认：
####      1.1、系统：
            Ubuntu 16.04 / Ubuntu 18.04
####      1.2、ROS版本：
            Kinetic / Melodic

## 2.电脑，工控机开机设置
####      2.1、rc.local文件确认：
            查看/etc/目录下有没有 rc.local文件，如果没有 sudo cp rc.local /etc
            如果存在rc.local文件，则在 rc.local的 exit 0上面加入 以下内容后保存。

            sleep 2
            sudo ip link set can0 type can bitrate 500000
            sudo ip link set can0 up

####      2.2、开机前CAN卡连接确认：
            开机或者重启之前，CAN卡已经接到工控机的USB口。

####      2.3、设置成功信号：
            看到RX和TX灯亮了就表示设置成功。
            
## 3.CAN卡连接与设置
####      3.1、底盘CAN卡连接：
            底盘的CAN-H连接到CAN卡接插头的CANH，底盘的CAN-L连接到Can卡接插头的CANL，如下图：
            
![](https://github.com/kefangkele/FW-max-ros/blob/main/images/CAN_Connection.png?raw=true)

####      3.2、电脑与机器人通信连接：
            底盘CAN卡连接完成后，将CAN卡usb线一端直接插到电脑usb口，完成CAN卡与电脑的连接，连接完成后，PWR灯亮。
####      3.3、USB是否连接成功检查：
            打开终端，输入指令：  lsusb  在输出信息中看到以下图片所示内容，则表示CAN能够正常使用。

![](https://github.com/kefangkele/FW-max-ros/blob/main/images/terminal_state.png?raw=true)  

####      3.4、通信启动：
            打开一个新的终端，输入指令：
            sudo ip  link  set  can0  type  can  bitrate  500000 & sudo ip  link  set  can0  up
            此时，若底盘处于上电状态，蓝色RX灯会闪烁，同时红色TX灯也会亮，RX灯闪烁表示Can卡接收到数据，TX灯闪烁表示发送数据。

## 4.通信数据查看
####      4.1 安装can-utils工具，请执行以下指令：
            sudo apt-get install can-utils
####      4.2 查看数据指令：
            candump can0
####      4.3、正常打印信息：
      

![](https://github.com/kefangkele/FW-max-ros/blob/main/images/candump_print.png?raw=true)

## 5.ROS驱动包运行
####      5.1、文件结构：
      
![](https://github.com/kefangkele/FW-max-ros/blob/main/images/doc_tree.png?raw=true)

####      5.2、编译工作空间：
            在你的工作空间中打开终端/在终端中进入你的工作空间，让终端处于工作空间级，再执行指令：
            catkin build
####      5.3、设置临时环境变量：
            在要运行程序的终端中执行指令：
            source ~/你的工作空间(例：5.1-ros2_ws)/devel/setup.bash
            
            或者让要运行程序的终端处于工作空间级，执行指令：
            source devel/setup.bash
            
![](https://github.com/kefangkele/FW-max-ros/blob/main/images/source.png?raw=true)

####      5.4、ROS驱动启动：
            在你刚才输入了临时环境变量的终端中执行指令：
            roslaunch yhs_can_control yhs_can_control.launch
            
            
####      5.5、终端打印出的运行成功信息：

![](https://github.com/kefangkele/FW-max-ros/blob/main/images/node_print.png?raw=true)  

## 6.运动测试：
####       6.1、在测试之前，建议先把车架起来，或者向底盘下发较小的速度，例如0.03。
####       6.2、打开新的终端，进入你的工作空间目录(例：5.1-ros2_ws)，执行指令
                        source devel/setup.bash
                   或者不进人工作空间目录，执行指令
                        source ~/你的工作空间(例：5.1-ros2_ws)/devel/setup.bash
####       6.3、查看话题数据
                        rostopic echo /ctrl_fb
####       6.4、执行完6.3的指令后在终端看到反馈的数据不断刷新，说明ROS驱动包运行正常。
####       6.5、下发指令控制底盘运动
              6.5.1、打开新的终端，进入你的工作空间目录，设置临时环境变量：
                        source devel/setup.bash
                     或者不进人工作空间目录，执行指令
                        source ~/你的工作空间(例：5.1-ros2_ws)/devel/setup.bash
              6.5.2、此处指令不执行，先输入终端
                        rostopic  pub  -r  100  /ctrl_cmd
              6.5.3、后面的内容可以按tab键补全，补全之后，要输入档位、速度和转向角，注意角度的单位是度不是弧度，输入完成后敲回车，将遥控器切换到自动挡，这时候就可以看到CAN卡上的红色和蓝色灯都在闪烁，底盘开始运动。
      
      
