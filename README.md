# Configure Multiple Github Accounts on Windows

### Table of Contents

1. [Create SSH key for each account](#create-ssh-key)  
2. [Add SSH keys to SSH Agent](#add-ssh)  
3. [Add SSH public key to the Github](#add-ssh-github)  
4. [Create a Config File](#create-config-file)  
5. [Cloning using different accounts](#clone-different-accounts)
6. [Commits and Push using different accounts](#commits-push-different-account)  



### Create SSH key for each account
(#create-ssh-key)

Example Accounts: 

**https:/<span></span>/github.com<span></span>/user-a**
**https:/<span></span>/github.com<span></span>/user-b**. 

Open the terminal

First make sure your current directory is your **.ssh** folder.
```sh
     $ cd C:/users/{YOUR_USERNAME}/.ssh
```

Generate ssh keys
```sh
     ssh-keygen -t rsa -C "your-email-address" -f "git-username"
```

**ssh-keygen**: This is the command-line utility for generating SSH keys.

**-t rsa**: Specifies the type of key to create. In this case, it specifies RSA as the type of encryption to be used for generating the key pair.

**-C** "your-email-address": This option adds a comment to the key. It's usually used to associate the key with an identifier or label, often an email address. Replace "your-email-address" with your actual email.

**-f "github-username"**: Specifies the filename for the generated key. Replace "github-username" with a filename of your choice. This is often used when you want to generate multiple keys for different purposes or services.


#### Example
```sh
     ssh-keygen -t rsa -C "user-a@gmail.com" -f "git-user-a"
     ssh-keygen -t rsa -C "user-b@gmail.com" -f "git-user-b"
```

After entering the command the terminal will ask for passphrase, you can leave it empty or specify a pass. <b>If you put a password, which is recommended, you have to type this password on every pull/push.</b>


> Now after creating the keys, you have 4 files in the .ssh folder.  

>The public key will have an extention __.pub__. The private key will be there without any extention both having same name which you have passed after __-f__ option in the above command.


### Add SSH keys to SSH Agent
(#add-ssh)

On Windows, you have to activate ssh-agent. So, you powershell with Admin permission.

Configure it to start automatically.
```
Get-Service ssh-agent | Set-Service -StartupType Automatic
```

Start the service
```sh
    Start-Service ssh-agent
```

This should return a status of Running
```sh
    Get-Service ssh-agent
```

Now we have to add the PRIVATE keys into the SSH Agent.
```sh
     ssh-add C:\\Users\\{YOUR_USERNAME}\\.ssh\\git-user-a
     ssh-add C:\\Users\\{YOUR_USERNAME}\\.ssh\\git-user-b
```

You can read more about adding ssh-agent on Windows [here.](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement)


### Add SSH public key to the Github
(#add-ssh-github)
Now you have to add the PUBLIC keys to the corresponding github accounts.

For doing this we need to:

__1. Copy the public key__

Just open the __.pub__ file with Visual Studio Code and copy the content.

__2. Paste the public key on Github__

* Sign in to Github Account
* Goto **Settings** > **SSH and GPG keys** > **New SSH Key**
* Paste your copied public key and give it a Title of your choice. <b>Make sure to remove any space or new line at the end.</b>


### Create a Config File
(#create-config-file)

The **~/.ssh/config** file allows us specify many config options for SSH.

If **config** file not already exists then create one (make sure you are in **C:\\Users\\{YOUR_USERNAME}\\.ssh** directory).

Create a file <b>without extension</b> on Windows

1. Open Notepad 
2. Save it with the name "config" <b>Don't forget the "</b>
3. Select extension "All files (.)"
4. Save it and close it

Now we need to add these lines to the file, each block corresponding to each account we created earlier.
```config
Host git-user-a
    HostName github.com
    User git
    IdentityFile C:/Users/{YOUR_USERNAME}/.ssh/user-a

Host git-user-b
    HostName github.com
    User git
    IdentityFile C:/Users/{YOUR_USERNAME}/.ssh/user-b
```

**Host** is the nickename for this configuration
**HostName** is the github website
**User** is git
**IdentifyFile** your config and private keys path


### Cloning using different accounts
(#clone-different-accounts)

Make sure to close your current terminal and open a new one so changes takes effect.

For Example:
To clone a repository:
 ```git
     git clone git@{host-nickname-on-config-file}-{your-username}:{owner-user-name}/{the-repo-name}.git

     [e.g.] git clone git@git-user-a:user-a/repository-to-clone.git
 ```

 <br>

### Commits and Push using different accounts
(#commits-push-different-account)

To ensure your commits and pushes from each repository uses the correct user — you have to configure **user.email** and **user.name** in every repository freshly cloned or existing before.

To do this use the following commands.

```git
     git config user.email "user-a@gmail.com"
     git config user.name "User A"
```


Change Origin on already cloned repositories:
```git
     git remote set-url origin git@{host-nickname-on-config-file}-{your-username}:{owner-user-name}/{the-repo-name}.git
```

Now you can use:
```git
     git push
     
     git pull
```

