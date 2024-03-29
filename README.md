# Docker_Robotics
1. `curl -fsSL https://get.docker.com -o get-docker.sh` it download the script for us
2. `sudo sh ./get-docker.sh`
3. `sudo groupadd docker`
   create the docker group
4. `sudo usermod -aG docker $USER`
   add your user to the docker group
5. `systemctl is-enabled docker`
   check the server enabled
6. `sudo systemctl enable docker.service` `sudo systemctl enable containerd.service`
   if not use these commands then reboot the computer
   
Interacting with docker
----------------------
8. `docker run hello-world`
   it will automatically pull the image is it is not available locally and run it
9. `docker image ls` list the all the images
10. `docker image pull ros:humble` this will pull humble variant from ros
11. `docker images`
12. `docker image rm hello-world` will receive an error message saying cant delete for that you have to force the command
13. `docker image rm -f hello-world` remove forcibly
14. `docker run ros:humble` no output will be there because once started it runs something and stops automatically. This command is usefull when deploying the final software version
15. `docker run -it ros:humble` this enables us to interact and develop within the humble system. here -i: interactive -t: give me the terminal
16. `ls` a new promt will be opened within the docker
17. `touch nisath` create a new file within the docker
18. `docker container ls` give the list of running container
19. `docker container stop loving_bohr` it stops the container named as loving_bohr
20. `docker ps` shortcut of `docker container ls` gives the running container list
21. `docker ps -a` gives the list of closed and running container list
22. `docker container start -i loving_bohr` starts the previously closed container
23. `docker run -it ros:humble` creates ne container check the name using `docker ps`. but the created file nisath wont be there as it is new container
24. `docker container rm loving_bohr` this command removes the container. so we cant access it again
25. `docker container prune` this removes all the stopped containers. check using `docker ps -a`
26. To start and remove the container automatically use `docker run --rm --name<container_name> <image_name>`
27. To open new terminal inside the container `docker exec -it goofy_allen /bin/bash`
28. To run single command inside the docker `docker exec -it goofy_allen ls`

Writing a Docker file
---------------------
We loose any changes made to the environment everytime we run the container. No way to get our own files and data. to overcome this we need to create our own image. 

1. Open a new folder in the desktop of the machine. Inside the folder create a Dockerfile in the vscode.
2. Download the Docker extension in the vscode
3. pull the repository from this github
4. build the docker file `docker image build -t my_image .` the dot represents the current path to the Dockerfile created
5. check the files `docker images`
6. run the my_image docker container `docker run -it my_image`
7. run the following command inside the new ccommand window `ls`
8. edit the site_config using nano `nano site_config/my_config.yaml`
9. closing the container `ctrl + c then ctrl + D`

Sharing the file from PC to container
-------------------------------------
Create a folder named my_code in the desktop. my_code --> src --> something.py.

I am intended to create this src folder as my_source_code/something.py inside container. we can do this in three ways.

1. `docker run -it -v $PWD/src:/my_source_code my_image` check the files using `ls` to confirm that file has been copied. Here PWD is `~/Desktop/my_code`
2. `cd my_source_code/` and create a new file called `nano new_file.yaml` then now check the main folder created in the PC called my_folder. In this way we can create something in container and store them for furthur use.
3. exit the container and go to `cd src/`
4. `ls -la` this exits information about the ownership of the files in the directory. Here, there is a problem that the file created cannot be opened by the user in the PC

Running with different users
---------------------------
1. change the following in the Dockerfile `ros:humble` to `osrf/ros:humble-desktop-full`
2. check the user id number for the user where the somthing.py is assigned . use `ls -ln` 
3. To add a user to group is `usermod -aG <group> <user>`
4. update the Dockerfile with `https://github.com/joshnewans/dockerfile_example/blob/main/Dockerfile`
5. build the docker image `docker build -t my_image .` for this instance avoid the following in the image file
   
`# Copy the entrypoint and bashrc scripts so we have our container's environment set up correctly
COPY entrypoint.sh /entrypoint.sh
COPY .bashrc /home/${USERNAME}/.bashrc
Set up entrypoint and default command
ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
CMD ["bash"]`

6. under `my_code` run `docker run -it --user ros -v $PWD/src:/my_source_code my_image` source the file something.py inside the container
7. create a new file inside `my_source_code` called as `newer_file` use sudo nano and touch to see the user name difference
8. run `ls -l` to see the user information of the files available
9. if you run the same code `ls -l` in the PC you will be seeing different user name but the uid will be same
10. rebuild our image `docker build -t my_image .` then rerun `docker run -it --user ros -v $PWD/src:/my_source_code my_image`
11. `sudo nano` inside the container. type something and save the file and then `ls -l` confirm the file

Networking
-----------
1. Telling the docker to share the network with the host, also add the ipc `docker run -it --user ros --network=host --ipc=host -v $PWD/src:/my_source_code my_image`
2. notice can be made in the user id in the command line 

Making an entrypoint script
---------------------------
1. create an entrypoint.sh in the directory `~/Desktop/docker_robotics/my_project`
2. update the dockerfile with the following lines,
   `Set up entrypoint and default command
   COPY entrypoint.sh /entrypoint.sh
   ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
   CMD ["bash"]`
4. rerun the docker `docker run -it --user ros --network=host --ipc=host -v $PWD/src:/my_source_code my_image ros2 topic list`
   we can add arguments at the end of this docker image running code

GUI Programs
-------------
1. `xhost +` `xhost +local` `xhost +local:root` `xhost -local:root` `xhost -` operations related to adding and removing all groups, local group and certain users.
2. run the following commands to run rviz2 on container but if you have any problem with GUI app or 3D applications use the picture with `3D_dockerApplication` in ROS folder in Google drive. `docker run -it --user ros --network=host --ipc=host -v $PWD/src:/my_source_code -v /tmp/.X11-unix:/tmp/.X11-unix:rw --env=DISPLAY my_image`
`

Locale and Timezone
-------------------
Installing language and time zone when creating docker image from scratch is must. Thus use the codes in the picture with `LocaleandTimezone` in ROS folder in Google drive.

Argument Completion
-------------------
1. When using double tap to figure available options for the argument we are passing, it may end up giving wrong output inside the container. 
2. Thus create a file inside `~/Desktop/docker_robotics/my_project` named bashrc.
3. include the following command to dockerfile
   `COPY .bashrc /home/${USERNAME}/.bashrc`
4. still any problem exists with autocompletion. add the following files in the dockerfile,
   `apt-get install -y\
    bash-completion \
    python3_argcomplete`

Device in Docker
----------------
Here will be seeing different devices to be conrolled and used in docker which is typically not so simple. they are,
1. connecting Game pad
2. Depth Camera
3. Serial device

- usually privileged mode is typically used to map the device and use them quickly. but on the other hand it affects the performance of docker. Thus, this is not recommended to do so.
- devices are looked as files in linux systems
- these files are available in `ls /dev`
- you can see how the input from the mouse is taken and interpreted. `ls -l /dev/input/mouse3`
- type in the following code to see the changes in the files in real time `sudo xxd /dev/input/mouse3`
- single device can have multiple files like this interface. for example `sudo xxd /dev/input/event10`
- you can see the all the devices connected along with the file associated `ls -l /dev/input/by-id`
- To get all the key information about the driver of each devices used is by `ls -l /dev/input`

Connecting a USB Game pad:
1. run the command in the PC terminal `jstest-gtk` which gives information about controller file directory and real time tracking of joysticks movements. take a note of the directory and file name in jstest-gtk console
2. run the my_image container `docker run -it --user ros --network=host --ipc=host -v $PWD/src:/my_source_code -v /tmp/.X11-unix:/tmp/.X11-unix:rw --env=DISPLAY my_image` then run `jstest-gtk` inside the container which `will failed to load the gamepad`


Docker in Visual Studio
-----------------------
1. Install `Remote Development and Docker` extension in the visual studio
2. Open a folder in the local host
3. check your ubuntu version `lsb_release -a`
4. go to the command platte or remote connection button in the bottom left corner and select `reopen in container` and search for `ros by ijnek` and  select the distro needed
5. you can notice the change in the bottom left corner button
6. type the following to ensure the ros distro `echo $ROS_DISTRO`
7. once container created will open a folder called `.devcontainer` inside will have the following two files `Dockerfile` and `devcontainer.json`
8. Docker file have the build/development tools and dependencies.
9. .json file will have the runtime argument and other parameters related to environment
10. add another directory in the mount under .json file as follows `source=/home/deck/Desktop,target=/desktop,type=bind` then rebuild the container
11. To rebuild you can select the button in the bottom left corner
12. tip 1: you will have two type of container file in the history. one is the local and the other is the container one which will start from where we left in the container work
13. tip 2: to use the same docker container, install the things in the docker file because when we rebuild the container we will loose the things
14. tip 3: open the local contianer, you can use the `remote explorer` and then `dev container`
15. tip 4: we can setup container by selecting `Add Dev Container Configuration Files` in the command palatte and then we can add the dependency in the docker files before rebuilding the container file
16. tip 5: what if you have the docker file already, navigate to the folder in which the docker file is then select `reopen docker file` and select `from docker file`
17. tip 6: `ctrl+shift+P` to open the command palatte then type in the needed file in the system
18. tip 7: you can go to extensions then see the installed packages in the local host and the container. also it will give the recommendations to install in relevant space. go to command palatte `configure recommended extensions` to share the recommended packages to the other user of the program

Running GUI programs:
1. open the terminal and type `xhost +local:` then rebuild the conatiner and reopen the container in the vscode. Now you should be able to run the gui programs like `rviz2`


