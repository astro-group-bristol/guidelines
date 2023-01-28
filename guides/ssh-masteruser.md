# How to be an SSH Master.
## .ssh/config - SSH config file

You probaly know how to use the ```ssh``` command from the terminal to access the astrophysics servers, but the last thing you would want to do is type it out everytime. This gets just gets tedious. 

Instead we can save our ssh connection details in the ssh configuration file. To do this you can ...

1. Use a text editor to open the ssh config file on your personal computer.
   ```bash
   vim ~/.ssh/config 
   ```
   You can use any text editor, here I have used ```vim```. ```~``` makes sure you are referencing your home directory. 

2. You should have an empty file, but if not just place the following underneath what is already there. In this file we can add details of different ssh destinations and name them. This follows the structure of the following:
   ```bash
   Host HOSTNAME
      SSH_OPTION value
      SSH_OPTION value

   ```
3. An example configuration you might want follows:
   ```bash
   Host typhon
        HostName TYPHON_ADDRESS
        User USERNAME
        ProxyJump aquila
    
    Host aquila
        HostName AQUILA_ADDRESS
        User USERNAME
   ```
   Make sure you replace ```AQUILA_URL```,```TYPHON_ADDRESS``` and ```USERNAME``` with their addresses and your username and save the file. With ```vim``` this can be done with ```:wq``` in command mode (to enter command mode press ESC key). Then try it out! Simply type ```ssh aquila``` or ```ssh typhon``` into the commandline, insert your password(s) and your in! 

## Using SSH Keys

If you do not want to keep inserting your password you can instead use a SSH key to authenicate your connection.

First you will need to generate a private and public key (this is simply a long sequence of numbers and letters). You can do this using the following.
```bash
$ ssh-keygen -t rsa
```
