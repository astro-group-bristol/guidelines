# Using VSCode's remote SSH features with the astrophysics servers

Microsoft's VSCode is gaining remote editor support, particularly [remote development over ssh](https://code.visualstudio.com/docs/remote/ssh-tutorial), which can be used with the astrophysics servers at Bristol.

<!--BEGIN TOC-->

## Table of Contents

1. [Setting up the VSCode SSH extension](#setting-up-the-vscode-ssh-extension)
2. [SSH tunnelling](#ssh-tunnelling)
3. [Connecting to the tunnel in VSCode](#connecting-to-the-tunnel-in-vscode)
4. [Using the extension](#using-the-extension)
   1. [Navigating and editing files](#navigating-and-editing-files)
   2. [Opening terminals](#opening-terminals)
   3. [Executing code remotely](#executing-code-remotely)
5. [Disconnecting](#disconnecting)

<!--END TOC-->

For those unfamiliar, a brief summary of remote development:

- gives you secure access to the filesystem of a remote machine
- execute code directly on the remote machine
- edit in a text editor or IDE on your local machine

Microsoft's VSCode is particularly well suited for this, as the extension and language support system provided by the community is still supported via the remote development, which means it _feels_ like your working on your local machine, but _executing_ on a more powerful server.

This guide briefly details how to get this set-up. For illustrative purposes, we want to work with the GPU on Typhon, but we want to access Typhon via the login server Aquila.

```
                        ┌┐    BRISTOL SERVERS
                        ││
    ┌──────┐        ┌───┴┴─────┐        ┌──────────┐
    │      ├───────►│          ├───────►│          │
    │  PC  │        │  AQUILA  │        │  TYPHON  │
    │      │◄───────┤          │◄───────┤          │
    └──────┘        └───┬┬─────┘        └──────────┘
                        ││
                        └┘
```

_Domain names have been omitted in this public repository._

## Setting up the VSCode SSH extension <a id="toc-tag-mdtoc" name="setting-up-the-vscode-ssh-extension"></a>

Start VSCode, and click on the "Extensions" tab on the left:

<p align="center">
<img height="200" src="../assets/vs-code-extensions.png"/>
</p>

Search for "SSH" and install the "Remote - SSH" extension:

<p align="center">
<img width="900" src="../assets/remote-ssh-extension.png"/>
</p>

Once installed, you will have a new status icon in the bottom left corner of VSCode

<p align="center">
<img width="200" src="../assets/remote-status-bar.png"/>
</p>

Clicking on this will bring up the SSH remote connection dialog. From here, we can enter the address of a remote machine, and VSCode will open a new window with a remote session.

## SSH tunnelling <a id="toc-tag-mdtoc" name="ssh-tunnelling"></a>

Because we can't access Typhon directly, we have to take an additional step to login when not using the UoB WiFi:

- SSH to Aquila, then SSH to Typhon
- Connect to the [UoB VPN (sharepoint link)](https://uob.sharepoint.com/sites/itservices/SitePages/vpn-connect.aspx)

This section will focus on creating an SSH bridge through Aquila.

The `ssh` program supports _tunneling_, which allows a machine to forward ports via SSH to another machine. This can be used to make e.g. secure web-connection, or just to forward SSH traffic.

From the `ssh` manual, the `-L` flag allows us to forward, or _tunnel_, traffic from one machine through to another.

> ```
> -L [bind_address:]port:host:hostport
> -L [bind_address:]port:remote_socket
> -L local_socket:host:hostport
> -L local_socket:remote_socket
> ```

Our aim then is to expose a port on our local machine (PC), which will send traffic via `ssh` to Aquila, which will in turn forward this traffic to Typhon. We can do this with

```bash
ssh -N -L localhost:12001:TYPHON:22 USER@AQUILA
```

Replace `TYPHON`, `AQUILA`, and `USER` with the correct domain names and your Bristol username ("ab12345").

Quickly dissecting this command:

- `-N`: Do not execute a remote command on Aquila.
- `-L localhost:12001:TYPHON:22` bind port `12001` on our local machine (PC), such that when a connection is made to this port, it will be forwarded, via Aquila, to Typhon at port 22 (the default SSH port).
- `USER@AQUILA` the bridge, or _tunnel_, host where we want to pass our traffic through.

Note, this will login to Typhon with the _same username_ as `USER`. The command will also not display anything when run, and will appear to 'hang', but this is intended!

## Connecting to the tunnel in VSCode <a id="toc-tag-mdtoc" name="connecting-to-the-tunnel-in-vscode"></a>

With the tunnel running in a terminal, we now return to VSCode and can open a remote session on Typhon.

To do this, we click on the green remote session button in the bottom left corner:

<p align="center">
<img width="600" src="../assets/remote-connect.png"/>
</p>

Select "Connect to Host", and enter point the SSH session to our local port:

<p align="center">
<img width="600" src="../assets/remote-localhost.png"/>
</p>

Hit enter to bring up the new remote session. You may be prompted about trusting `localhost` keys the first time you connect -- this is perfectly fine, and just click "yes".

Once connected, in the bottom left corner the connection should be displayed:

<p align="center">
<img width="400" src="../assets/remote-connection.png"/>
</p>

## Connecting through VSCode

VSCode also has the ability to access the astro servers with an `ssh` command in VSCode. With the previous "Connect to Host..." option above, select "+ Add New SSH Host...". This will show a prompt to enter a command. You will need to enter something like the following:

```bash
ssh -N -L localhost:12001:TYPHON:22 -J USER:AQUILA USER:TYPHON
```

The addition of `-J` makes `AQUILA` a proxy and `TYPHON` the final destination. VSCode will make a change to your '.ssh' file, saving the command. You will find the host in the same way as above, you will also be prompted for your passwords to both the proxy and desired server when you try to connect.

## Using the extension <a id="toc-tag-mdtoc" name="using-the-extension"></a>

Now that we are set up, there are numerous things we can do.

### Navigating and editing files <a id="toc-tag-mdtoc" name="navigating-and-editing-files"></a>

To open the file explorer, we click on the file icon in the left navigation bar at the top:

<p align="center">
<img width="600" src="../assets/remote-file.png"/>
</p>

Click "Open Folder" and either use the navigation dialog, or enter the path to a folder you want to open. The entire folder is then used as the VSCode workspace, allowing you to have multiple files open at once:

<p align="center">
<img src="../assets/remote-workspace.png"/>
</p>

VSCode's extensions support displaying many different file types, including images, videos, spreadsheets, databases, rendered markdown, HTML, and more, all in your remote SSH session.

### Opening terminals <a id="toc-tag-mdtoc" name="opening-terminals"></a>

We can open a new terminal from the "Terminal" menu at the top of our screen:

<p align="center">
<img width="300" src="../assets/remote-terminal-1.png"/>
</p>

This shell will be running on Typhon directly. To add more terminals, or change the terminal type, click the little "+" icon:

<p align="center">
<img width="300" src="../assets/remote-terminal-2.png"/>
</p>

### Executing code remotely <a id="toc-tag-mdtoc" name="executing-code-remotely"></a>

Any command executed in the terminals opened above will now automatically be executed on the remote server.

Any extensions installed in the remote session will also automatically be installed on Typhon, allowing you to run language extensions (such as Python or Julia) directly on the remote machine.

## Disconnecting <a id="toc-tag-mdtoc" name="disconnecting"></a>

To disconnect, simply close the VSCode window, and navigate back to the terminal running the SSH tunnel and press `Ctrl+c` to cancel the process.
