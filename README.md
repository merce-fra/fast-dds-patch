# Fast-DDS white list setup

Discovery mechanisms are often required to dynamically list all ROS2 communication topics between nodes living on the network. However, we believe we encountered issues when multiple remote agents perform this routine altogether on limited resources (e.g., a dozen of raspberry pi). To limit the CPU peak we observed, and probably to improve scheduling and wireless packet transfer we propose to add a white list mechanism to allow specific topics present on the list to be targeted by the discovery service onboard the remote agents without being polluted by traffic running solely on the host machine. This allowed us to perform our experiments with multiple agents embedding ROS2 Humble on armbian boards.

## Installation

Follow the procedure to modify Fast-DDS on **your remote agents not on the host machine**:
* Go to your ROS2 src folder and create a backup of your current Fast-DDS repository
* Download Fast-DDS repo (targeting our current tag but it should work with the most recent version too)
* apply the patch to the EDPClient.cpp file

```
$ cd <your_ros2_ws>/src
$ mv eProsima/Fast-DDS eProsima/Fast-DDS_backup (if present)
$ git clone git@github.com:merce-fra/fast-dds-patch.git
$ vcs import ../ < fast-dds.repos
$ cp EDPClient.patch ../eProsima/Fast-DDS
$ cd ../eProsima/Fast-DDS 
$ git apply EDPClient.patch
```

A white list has now been added to the file EDPClient.cpp. Go to this file to allow only the necessary traffic for your remote nodes.
For example you can add "/cmd_vel" or "/clock" to the list. Then, all topics containing these entries will be allowed to exchange packets. There is no need to add the topics for all namespaces (e.g., "/robot_1/cmd_vel", "/robot_2/cmd_vel", ... ).

Now you can recompile the Fast-DDS package with

```
cd <your_ros2_ws>
colcon build --packages-select fastrtps
```

Note: By running Wireshark you should observe a lower traffic peak when launching your experiment.
