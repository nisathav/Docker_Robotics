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
