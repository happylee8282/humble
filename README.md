# humble
You're reading the documentation for an older, but still supported, version of ROS 2. For information on the latest version, please have a look at Kilted.

Ubuntu (source)
Table of Contents

System requirements

System setup

Set locale

Add the ROS 2 apt repository

Install development tools and ROS tools

Get ROS 2 code

Install dependencies using rosdep

Install additional DDS implementations (optional)

Build the code in the workspace

Environment setup

Source the setup script

Try some examples

Next steps after installing

Using the ROS 1 bridge

Additional RMW implementations (optional)

Alternate compilers

Clang

Stay up to date

Troubleshooting

Uninstall

System requirements
The current Debian-based target platforms for Humble Hawksbill are:

Tier 1: Ubuntu Linux - Jammy (22.04) 64-bit

Tier 3: Ubuntu Linux - Focal (20.04) 64-bit

Tier 3: Debian Linux - Bullseye (11) 64-bit

Other Linux platforms with varying support levels include:

Arch Linux, see alternate instructions

Fedora Linux, see alternate instructions

OpenEmbedded / webOS OSE, see alternate instructions

As defined in REP 2000.

System setup
Set locale
Make sure you have a locale which supports UTF-8. If you are in a minimal environment (such as a docker container), the locale may be something minimal like POSIX. We test with the following settings. However, it should be fine if you’re using a different UTF-8 supported locale.

locale  # check for UTF-8

sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8

locale  # verify settings
Add the ROS 2 apt repository
You will need to add the ROS 2 apt repository to your system.

First ensure that the Ubuntu Universe repository is enabled.

sudo apt install software-properties-common
sudo add-apt-repository universe
The ros-apt-source packages provide keys and apt source configuration for the various ROS repositories.

Installing the ros2-apt-source package will configure ROS 2 repositories for your system. Updates to repository configuration will occur automatically when new versions of this package are released to the ROS repositories.

sudo apt update && sudo apt install curl -y
export ROS_APT_SOURCE_VERSION=$(curl -s https://api.github.com/repos/ros-infrastructure/ros-apt-source/releases/latest | grep -F "tag_name" | awk -F'"' '{print $4}')
curl -L -o /tmp/ros2-apt-source.deb "https://github.com/ros-infrastructure/ros-apt-source/releases/download/${ROS_APT_SOURCE_VERSION}/ros2-apt-source_${ROS_APT_SOURCE_VERSION}.$(. /etc/os-release && echo ${UBUNTU_CODENAME:-${VERSION_CODENAME}})_all.deb"
sudo dpkg -i /tmp/ros2-apt-source.deb
Install development tools and ROS tools
Install common packages.

sudo apt update && sudo apt install -y \
  python3-flake8-docstrings \
  python3-pip \
  python3-pytest-cov \
  ros-dev-tools
Install packages according to your Ubuntu version.

Ubuntu 22.04 LTS and laterUbuntu 20.04 LTS
sudo apt install -y \
   python3-flake8-blind-except \
   python3-flake8-builtins \
   python3-flake8-class-newline \
   python3-flake8-comprehensions \
   python3-flake8-deprecated \
   python3-flake8-import-order \
   python3-flake8-quotes \
   python3-pytest-repeat \
   python3-pytest-rerunfailures
Get ROS 2 code
Create a workspace and clone all repos:

mkdir -p ~/ros2_humble/src
cd ~/ros2_humble
vcs import --input https://raw.githubusercontent.com/ros2/ros2/humble/ros2.repos src
Install dependencies using rosdep
ROS 2 packages are built on frequently updated Ubuntu systems. It is always recommended that you ensure your system is up to date before installing new packages.

sudo apt upgrade
sudo rosdep init
rosdep update
rosdep install --from-paths src --ignore-src -y --skip-keys "fastcdr rti-connext-dds-6.0.1 urdfdom_headers"
Note: If you’re using a distribution that is based on Ubuntu (like Linux Mint) but does not identify itself as such, you’ll get an error message like Unsupported OS [mint]. In this case append --os=ubuntu:jammy to the above command.

Install additional DDS implementations (optional)
If you would like to use another DDS or RTPS vendor besides the default, you can find instructions here.

Build the code in the workspace
If you have already installed ROS 2 another way (either via debs or the binary distribution), make sure that you run the below commands in a fresh environment that does not have those other installations sourced. Also ensure that you do not have source /opt/ros/${ROS_DISTRO}/setup.bash in your .bashrc. You can make sure that ROS 2 is not sourced with the command printenv | grep -i ROS. The output should be empty.

More info on working with a ROS workspace can be found in this tutorial.

cd ~/ros2_humble/
colcon build --symlink-install
Note: if you are having trouble compiling all examples and this is preventing you from completing a successful build, you can use COLCON_IGNORE in the same manner as CATKIN_IGNORE to ignore the subtree or remove the folder from the workspace. Take for instance: you would like to avoid installing the large OpenCV library. Well then simply run touch COLCON_IGNORE in the cam2image demo directory to leave it out of the build process.

Environment setup
Source the setup script
Set up your environment by sourcing the following file.

. ~/ros2_humble/install/local_setup.bash
Note

Replace .bash with your shell if you’re not using bash. Possible values are: setup.bash, setup.sh, setup.zsh.

Try some examples
In one terminal, source the setup file and then run a C++ talker:

. ~/ros2_humble/install/local_setup.bash
ros2 run demo_nodes_cpp talker
In another terminal source the setup file and then run a Python listener:

. ~/ros2_humble/install/local_setup.bash
ros2 run demo_nodes_py listener
You should see the talker saying that it’s Publishing messages and the listener saying I heard those messages. This verifies both the C++ and Python APIs are working properly. Hooray!

Next steps after installing
Continue with the tutorials and demos to configure your environment, create your own workspace and packages, and learn ROS 2 core concepts.

Using the ROS 1 bridge
The ROS 1 bridge can connect topics from ROS 1 to ROS 2 and vice-versa. See the dedicated documentation on how to build and use the ROS 1 bridge.

Additional RMW implementations (optional)
The default middleware that ROS 2 uses is Fast DDS, but the middleware (RMW) can be replaced at runtime. See the guide on how to work with multiple RMWs.

Alternate compilers
Using a different compiler besides gcc to compile ROS 2 is easy. If you set the environment variables CC and CXX to executables for a working C and C++ compiler, respectively, and retrigger CMake configuration (by using --cmake-force-configure or by deleting the packages you want to be affected), CMake will reconfigure and use the different compiler.

Clang
To configure CMake to detect and use Clang:

sudo apt install clang
export CC=clang
export CXX=clang++
colcon build --cmake-force-configure
Stay up to date
See Maintain source checkout to periodically refresh your source installation.

Troubleshooting
Troubleshooting techniques can be found here.

Uninstall
If you installed your workspace with colcon as instructed above, “uninstalling” could be just a matter of opening a new terminal and not sourcing the workspace’s setup file. This way, your environment will behave as though there is no Humble install on your system.

If you’re also trying to free up space, you can delete the entire workspace directory with:

rm -rf ~/ros2_humble
