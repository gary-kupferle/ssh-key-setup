
# Set up personal SSH keys on macOS

Note: This page was copied from https://support.atlassian.com/bitbucket-cloud/docs/set-up-personal-ssh-keys-on-macos,
but it is about the same as others for ssh setup.

The third-party Git Credential Manager (GCM) can be used as alternative method of connecting to Bitbucket Cloud from the Git CLI. If you do not want to configure SSH access for your Bitbucket Cloud account, you can download and install the GCM from Git Credential Manager on GitHub. Note that the GCM works over HTTPS, not SSH. Ensure your Git remotes are using HTTPS, such as:
git clone https://{username}@bitbucket.org/{workspace}/{repository}.git

The Secure Shell protocol (SSH) is used to create secure connections between your device and Bitbucket Cloud. The connection is authenticated using public SSH keys, which are derived from a private SSH key (also known as a private/public key pair). The secure (encrypted) connection is used to securely transmit your source code between your local device and Bitbucket Cloud. To set up your device for connecting Bitbucket Cloud using SSH, you need to:

1. Install OpenSSH on your device.
2. Start the SSH agent.
3. Create an SSH key pair.
4. Add your key to the SSH agent.
5. Provide Bitbucket Cloud with your public key.
6. Check that your SSH authentication works.

### Install OpenSSH on macOS
A version of OpenSSH should be pre-installed on macOS. To check if OpenSSH is installed, open a terminal and run:
```
ssh -V
```

The output should show the installed version of OpenSSH.

To use Homebrew to install a newer version of OpenSSH, run:
```
brew install openssh
```

To check that OpenSSH was installed successfully, run:
```
ssh -V
```

The output should show the installed version of OpenSSH.

## Start the SSH agent
To allow git to use your SSH key, an SSH agent needs to be running on your device.

To check if it is already running, run the ps command. If the ssh-agent is already running, it should appear in the output, such as:

```
$ ps -ax | grep ssh-agent
19998 ??         0:00.20 /usr/bin/ssh-agent -l
```

To start the agent, run:
`eval $(ssh-agent)`

You may need to add this command to your ~/.bashrc, ~/.zshrc, ~/.profile, or equivalent shell configuration file. Adding this command to a shell configuration file will ensure the agent is running when you open a terminal. 

### Create an SSH key pair
To create an SSH key pair:

Open a terminal and navigate to your home or user directory using cd, for example:
```
cd ~
```

Generate a SSH key pair using ssh-keygen, such as:
```
ssh-keygen -t ed25519 -b 4096 -C "{username@emaildomain.com}" -f {ssh-key-name}
```

Where:
  * `{username@emaildomain.com}` is the email address associated with the Bitbucket Cloud account, such as your work email account.
  * `{ssh-key-name}` is the output filename for the keys. We recommend using a identifiable name such as bitbucket_work.

When prompted to Enter passphrase, **you can either provide a password** or leave the password empty. If you input a password, **you will be prompted for this password each time SSH is used**, such as using Git command that contact Bitbucket Cloud (such as git push, git pull, and git fetch). Providing a password will prevent other users with access to the device from using your keys.

Once complete, ssh-keygen will output two files:
  * `{ssh-key-name}` — the private key.
  * `{ssh-key-name}.pub` — the public key.

### Add your key to the SSH agent
To add the SSH key to your SSH agent (ssh-agent):
1. Run the following command, replacing the {ssh-key-name} with the name of the private key:
`ssh-add ~/{ssh-key-name}`

2. To ensure the correct SSH key is used when connecting to Bitbucket, update or create your SSH configuration file `(~/.ssh/config)` with the following settings:

```
Host bitbucket.org
  AddKeysToAgent yes
  IdentityFile ~/.ssh/{ssh-key-name}
```

Where `{ssh-key-name}` is the location of the private key file once it has been added to the ssh-agent.

### Provide Bitbucket Cloud with your public key
To add an SSH key to your user account:
1. Select the Settings cog on the top navigation bar.
2. From the Settings dropdown menu, select Personal Bitbucket settings.
3. Under Security, select SSH keys.
4. Select Add key.
5. In the Add SSH key dialog, provide a Label to help you identify which key you are adding. For example, Work Laptop <Manufacturer> <Model>. A meaning full label will help you identify old or unwanted keys in the future.
6. Open the public SSH key file (public keys have the .pub file extension) in a text editor. The public key should be in the .ssh/ directory of your user (or home) directory. The contents will be similar to:
`ssh-ed25529 LLoWYaPswHzVqQ7L7B07LzIJbntgmHqrE40t17nGXL71QX9IoFGKYoF5pJKUMvR+DZotTm user@example.com`
7. Copy the contents of the public key file and paste the key into the Key field of the Add SSH key dialog.
8. Select Add key.
  * If the key is added successfully, the dialog will close and the key will be listed on the SSH keys page.
  * If you receive the error That **SSH key is invalid**, check that you copied the entire contents of the **public key** (`.pub` file).

### Check that your SSH authentication works
To test that the SSH key was added successfully, open a terminal on your device and run the following command:
`ssh -T git@bitbucket.org`

If SSH can successfully connect with Bitbucket using your SSH keys, the command will produce output similar to:

```
authenticated via ssh key.

You can use git to connect to Bitbucket. Shell access is disabled
```
