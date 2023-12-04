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
   
INTERACTING WITH DOCKER
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

Sharing the file from PC to container
-------------------------------------
Create a folder named my_code in the desktop. my_code --> src --> something.py.

I am intended to create this src folder as my_source_code/something.py inside container. we can do this in three ways.

1. `docker run -it -v $PWD/src:/my_source_code my_image` check the files using `ls` to confirm that file has been copied. Here PWD is `~/Desktop/my_code`
2. `cd my_source_code/` and create a new file called `nano new_file.yaml` then now check the main folder created in the PC called my_folder. In this way we can create something in container and store them for furthur use.
3. exit the container and go to `cd src/`
4. `ls -la` this exits information about the ownership of the files in the directory. Here, there is a problem that the file created cannt be opened by the user in the PC
