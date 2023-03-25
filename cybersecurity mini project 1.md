
# Step 1 (Prepare Virtual Box)
install a virtual machine in your computer add any operating system ubuntu/lubuntu/xubuntu whatever you preferred
![image](https://user-images.githubusercontent.com/62655613/227700302-4bc8f95f-18a0-4db9-a00a-1f796fba93ef.png)

# Step 2 (Prepare Operating System)
Take a snapshot (not screenshot) of the virtual machine and save it as “State 0”.
Delete everything in the computer with the following command as root.
```bash
 sudo rm -rf /*
```
And enjoy your beloved machine being destroyed in front of your eyes.
Restore the snapshot taken earlier which was saved as “State 0”.
Run update and upgrade:
```bash
sudo apt-get update && sudo apt-get upgrade
```
```bash
sudo apt-get dist-upgrade
```
Configure the network of the virtual machine to use a  host-only network.
```bash
ip addr
```
```bash
ifconfig
```
Change the hostname of the VM to “server”.
Clone the virtual machine, turn it on and change the hostname to “client”.


