# Fast-DDS white list setup

Discovery mechanisms are often required to dynamically list all ROS2 communication topics between nodes. However, we believe we encountered issues when multiple remote agents perform this routine altogether on limited resources (e.g., a dozen of raspberry pi). To limit the communication peak and probably improve wifi packet transfer we propose to add a white list mechanism to solely allow topics present in the list to be targeted by the discovery. This allowed us to perform our experiments with multiple agents embedding ROS2 Humble on armbian boards.

## Installation

Follow the procedure:
* Go to your ROS2 src folder and erase your current Fast-DDS repository
* Download Fast-DDS repo
* grant rights to the custom script
* apply the script
 
```
$ cd <your_ros2_ws>/src
$ rm eProsima/Fast-DDS (if present)
$ vcs import . < fast-dds.repos
$ cp EDPClient.patch eProsima/Fast-DDS
$ cd eProsima/Fast-DDS && git apply EDPClient.patch
```

A white list has been added to the file EDPClient.cpp. Go to this file to add the topics you want to allow between remote nodes.
For example you can add "/cmd_vel" or "/clock" to the list. Then, all topics containing these entries will be allowed to exchange packets. There is no need to add the topics for all namespaces (e.g., "/robot_1/cmd_vel", "/robot_2/cmd_vel", ... ). 

Now you can recompile the Fast-DDS. By running Wireshark you should see a much lower traffic peak.
