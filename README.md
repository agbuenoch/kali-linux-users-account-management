# kali-linux-users-account-management
This project implements how to securely manage user accounts in Kali, including changing login credentials, creating new users, switching between shells and users, renaming users, deleting users, and locking/unlocking passwords and login shells.

User account management is a foundational skill for anyone working with Kali Linux, whether you're a cybersecurity student, penetration tester, or ethical hacker. This project explores how to securely manage user accounts in Kali, from creating and renaming users to disabling logins and deleting users. While Kali is built for offensive security, proper user control is essential to maintain a secure and organised environment.

## Step 1: How to Change Login Credentials.
The default username and password that come preinstalled with the Kali Linux pre-built image are kali and kali, respectively. Let's change the password to something more secure while we maintain the username kali.

Open the Kali Linux terminal by clicking on the terminal as pointed to by the 1st arrow. The username and host/machine name is `kali` and `kali` as pointed to by the 2nd and 3rd arrow, respectively. Run `whoami` to check/view the current user logged in as pointed to by the 4th arrow.

Run `passwd` to change the kali username password by providing the current password and then the new password. The 5th arrow points out that the password was changed successfully.<br>
**View: [step1A](screenshots/step1A)**

The password for the kali username is now changed from `kali` to the new password we provided.

**Check if the current user can elevate to root privileges.**<br>
Run either the commands `id` or `groups`, or `groups kali`. You'll see something like this below as pointed to by the 1st arrow:<br>
`uid=1000(kali) gid=1000(kali) groups=1000(kali),27(sudo)`

Where:
- uid=0 refers to a root user.
- uid=1000 and above refers to a regular user.
But if `sudo` appears in the groups as pointed to by the 2nd arrow, it means this regular user can elevate its privileges, i.e it can use the `sudo` command and operate with root privileges.<br>
**View: [step1B](screenshots/step1B)**

## Step 2: Create New Users.
Let's view the current list of all users before we create new ones. Run `ls /home` to print out a list of all users in this Kali Linux system. As pointed out by the 1st arrow, there is currently only one user, which is `kali`.<br>
**View: [step2A](screenshots/step2A)**

Alternatively, we can run `cat /etc/passwd` to view detailed user information like the group and shells they belong to, compared to just listing the username as shown above. In Kali Linux, like all Linux distributions, all local users are listed in the system file.

The path `/etc/passwd` contains the list of all users. This file contains a record of every user account on the system. From the screenshot below, the 1st arrow points to three user account `root, daemon, and bin`. As underlined, the `root` user's home directory is `/root` and uses `zsh` as its login shell (this is one among many shell types like `bash, fsh, and ksh`). Where you see `/nologin` means that the user's login shell has been locked or disabled and cannot log in to the system.<br>
**View: [step2B1](screenshots/step2B1)**

The 2nd arrow points to another user called `kali`, the 3rd and 4th arrows point to the kali home directory and default login shell, respectively.<br>
**View: [step2B2](screenshots/step2B2)**

Each user is on a separate line with fields separated by colons `:` in this format below.<br>
`username : password : UID : GID : comment : home_directory : login_shell`

The table below explains the components of the kali username information above as pointed to by the 2nd, 3rd and 4th arrows.<br>
| Fields             | Descriptions                                                                                          |
|--------------------|-------------------------------------------------------------------------------------------------------|
| kali               | Username                                                                                              |
| x                  | Password placeholder (actual password is in /etc/shadow)                                              |
| 1000               | UID (If user ID = 0, it’s a root user, if ID=1000+ it’s a normal/regular user), 1000  GID (Group ID)  |
| ,,,                | Description/Comment (optional)                                                                        |
| /home/kali         | Home directory                                                                                        |
| /bin/zsh           | Default login shell                                                                         |

**NOTE:** Kali recently switched its default shell from `bash` to `zsh` to give users more power and flexibility.<br>
**View: [step2B4a](screenshots/step2B4a)<br>**
**View: [step2B4b](screenshots/step2B4b)**

**You can switch between shells at any time.**
Let's use the `demouser` to switch between the shells. Run `echo $SHELL` to view the `demouser` login shell as pointed to by the 1st arrow. Run `chsh -s /bin/zsh` to change the `demouser` login shell from `bash` to `zsh` as pointed to by the 2nd arrow, and you will be prompted for the `demouser` password.

To apply the changes, log out from the `demouser` as pointed by the 3rd arrow. The 4th arrow points to `kali` user after logging out from the `demouser`. Let’s log back in to `demouser` as pointed to by the 5th arrow. The 6th arrow points to the `demouser` currently logged in. Run `echo $SHELL` again to view that the shell has been changed successfully as pointed to by the 7th arrow<br>
**View: [step2B5](screenshots/step2B5)**

Currently, `demouser` login shell is  `zsh` as pointed to by the 1st arrow. Run `chsh -s /bin/bash` to change the shell to `bash`, the rest of the steps are similar to the steps just explained above. Logout by running `exit` and re-log in to the `demouser`, run `echo $SHELL` to confirm the changes have been applied.<br>
**View: [step2B6](screenshots/step2B6)**

**Where Are Passwords Stored?**
Passwords are not stored in `/etc/passwd` for security. Instead, they’re kept in `/etc/shadow`, readable only by `root` user. To view it, run `sudo cat /etc/shadow`, you’ll see encrypted password hashes.<br>
**View: [step2B7](screenshots/step2B7)**

Therefore, let's create two new users, `demouser` and `adminuser` and add `adminuser` to the `sudo` group, making it an admin/super user. By this, `adminuser` can use the `sudo` command and root user privileges. Ensure the usernames are spelt in lowercase letters.

Run `sudo adduser <username>` to create a new user. Each new user created would be assigned a User ID (`UID`) and Group ID (`GID`) ranging between `1000` and `59999`, as pointed to by the 1st arrow. A home directory of `/home/<username>` would be created for the new user as pointed to by the 2nd arrow. You will be prompted to set a password for the user as pointed to by the 3rd arrow. You have other information like the user's full name, which is optional, and you can press the `ENTER` key as pointed to by the 4th arrow to skip each entry.

For every new user created, a group bearing the same name as the username will be created, and the new user will be added to that group and also to an additional group called `users`. Therefore, `demouser` is added to a group called `demouser` and `users`, as pointed to by the 5th and 6th arrows, respectively.<br>
**View: [step2C1](screenshots/step2C1)**

Apply the same step above to create `adminuser`, and notice that `adminuser` is added to a group called `adminuser` and `users`, as pointed to by the 5th arrow and 6th arrows, respectively.<br>
**View: [step2C2](screenshots/step2C2)**

Run `sudo cat /etc/passwd` to view the two new users we created above. The 1st arrow points to the users we created, and the underlined `/bash` is the default login shell for the users.<br>
**View: [step2C3](screenshots/step2C3)**

From the screenshot below, we are currently operating as `kali` user. Run `echo $SHELL` to view the user login shell, which is `zsh` as pointed to by the 1st arrow.

The 2nd and 3rd arrows point to all the groups user kali is a member of, among the groups is `sudo`, which means user `kali` can switch to a root user capability.

The 4th and 5th arrows point to the groups `demouser` is a member of. Because `sudo` is not listed, the user `demouser` cannot use `sudo` command or switch to a root user capability.

The 6th and 7th arrows point to the groups the `adminuser` is a member of. At the moment, it cannot assume a `root` capability or privileges because it does not belong to the `sudo` group. Notice that the group called `sudo` is not listed among the groups.<br>
**View: [step2C4](screenshots/step2C4)**

Alternatively, you can run the command `groups` to see all the groups the current user `kali` belongs to, as pointed to by the 1st arrow.<br>
**View: [step2C5](screenshots/step2C5)**

Let’s make `adminuser` a root user. This will enable the `adminuser` to be able to assume a super/root user capability, which is to be able to run the `sudo` command, so let's add the `adminuser` to the `sudo` group. As pointed by the 1st arrow, provide the password of the user `kali` running the `sudo` command.

After adding `adminuser` to the `sudo` group, run `groups adminuser`, notice the `sudo` group have been added to the list of groups it belongs to, as pointed to by the 2nd arrow.<br>
**View: [step2C6](screenshots/step2C6)**

Command Breakdown:
`sudo` means run the command as a superuser (admin privileges).

`usermod` stands for user modify – it lets you change user account settings.

`-a` means append (don’t remove them from other groups). `-G` means Group (you’re specifying the group(s) the user should belong to).

`sudo` The group you're adding the user to — on Debian-based systems like Kali, the `sudo` group permits its members to run sudo commands.

`adminuser` This is the username of the account you're giving sudo access to.

## Step 3: Switch between users.
Run the command `su - <username>` to switch between users. You'll be prompted for that user’s password. The dash `-` loads the user’s full environment (like their shell settings).

Run the command `sudo su -` or `sudo -i` to switch from any user that has root privileges to the `default root user` (which always appears in `red`). 

But switching to the `default root user` from a `regular user` who does not have root privileges (i.e does not belong to the `sudo` group), just run `su -`, and you will be required to provide the `default root user` password, which by default does not exist unless you set it. Therefore, we will stick to this command: `sudo su -` or `sudo -i` instead.

To switch to a `default root user`, you will be prompted for the current user password and NOT the `default root user` password because it has no password set by default.

Let's switch to the user called `root` (i.e the `default root user`). As pointed to by the 1st arrow, you’ll be prompted for the `kali` user's password, not `root's` password (because root may not have a password set by default). A super/root privilege is required, hence, we must use sudo to run `su -`, as shown below. And because the user `kali` belongs to the `sudo` group, the `sudo` command can be used by the kali user.

After switching to the `default root user`, run the `pwd` to print the working directory, which is now `/root` as pointed to by the 2nd arrow.

To switch back to a regular user, run `su - <username>`, as pointed to by the 4th arrow, we are back to the `kali` user.<br>
**View: [step3A](screenshots/step3A)**

By default, Kali does not enable the ` default root` account with a password anymore. Therefore, if you try switching to `default root user` by running the command `su -` as pointed out by the 1st arrow, you will be asked for the ``default root user` password as pointed out by the 2nd arrow, which is NOT enabled by default. Therefore, the authentication will fail as pointed out by the 3rd arrow, since there is no password previously set for it.<br>
**View: [step3A1](screenshots/step3A1)**

We can manually set the `default root user` password using `sudo passwd root`, You’ll be prompted to enter and confirm a new root password. After that, we can switch to the `default root user` by running `su -` without using sudo. But this is not recommended for daily use. The best practice is to use `sudo` with a regular user instead of logging in as root directly, for security and auditing.

On this premise, because the `kali` user has `sudo` privileges (belongs to the `sudo` group), for best security practice, we will use `sudo` to switch or log in to the `default root user` by running `sudo su -`

So far, from the users we created, we can only switch to the `default root user` from the `kali` and `adminuser` users using `sudo` because they belong to the `sudo` group.

But once we've set a password for the `default root` account (which is not recommended), we can absolutely use just `su -` command from any regular user account to switch to the `default root user`.

If you’re logged in as one user and want to go back to your previous user session, just run `exit` to log out or `su - <username> without logging out from the current user session. The `-` loads the user’s full environment (like their shell settings).

Alternatively, you can switch to the `default root user` with `sudo -i` as pointed to by the 1st arrow. After providing the current user password, we are logged in as the root user, which appears in red. Exit the `default root user` by running `exit` as pointed to by the 2nd arrow.<br>
**View: [step3A2](screenshots/step3A2)**

Let's verify that the `adminuser` now has `sudo` privileges. As pointed out by the 1st arrow, switch to the `adminuser`. Remember that `adminuser` is a regular user who can elevate to `root` user/privilege. Therefore, if you run just `whoami`, it will return the regular username `adminuser` as pointed to by the 2nd and 3rd arrows. The command `pwd` will print the current working directory as pointed out by the 4th arrow. Meanwhile, if you run `sudo whoami`, it will return the `root` username as pointed to by the 5th arrow. Therefore, it is confirmed that `adminuser` can elevate to a `root` user/privileges.<br>
**View: [step3B](screenshots/step3B)**

Remember the user `kali` is a regular user as pointed to  by the 2nd arrow, who is also a member of `sudo` group as pointed to by the 1st arrow, which means it can elevate to a `root` user/privileges as pointed to  by the 3rd arrow, just like the `adminuser` we created above.<br>
**View: [step3B2](screenshots/step3B2)**

Let’s try using a root user privilege like running the command `sudo` with a user that is **NOT** in a `sudo` group, like `demouser`.

Switch to the `demouser` as pointed to by the 1st arrow. We run `pwd` to see the current user we are operating as, pointed to by the 2nd arrow. The 3rd arrow points to the group `demouser` is a member of; notice `sudo` is not listed. As pointed out by the 4th arrow, we tried using `sudo` command but because the current user `demouser` is not a member of the `sudo` group, we get the warning message pointed to by the 5th arrow.<br>
**View: [step3C](screenshots/step3C)**

## Step 4: Renaming a user.
We can change the `adminuser` name to something else, but it involves more than just renaming the user. We also need to update the home directory and permissions properly to avoid login issues.

**Let’s go step-by-step to safely rename the adminuser to myadmin.**
**⚠️ Important:** Don’t do this while logged in as the `adminuser` user. First, create another temporary admin user called `tmpadmin`, and log in as `tmpadmin` to perform the renaming.

Create a new user, `tmpadmin`, following the same steps as done in **Step 2 above**. The `tmpadmin` user will be placed inside both the `tmpadmin` and `users` groups as pointed to by the 3rd and 7th arrows.<br>
**View: [step4A](screenshots/step4A)**

After creating the new user, `tmpadmin`, run `sudo cat /etc/passwd` to verify the user `adminuser` is listed as pointed to by the 1st arrow with the home directory and default login shell underlined.<br>
**View: [step4A2](screenshots/step4A2)**

The `tmpadmin` created above is a regular user; let's add it to the `sudo` group so that it can be able to run the `sudo` command and elevate to `root` user/privileges. Run `sudo usermod -aG sudo tmpadmin` as pointed to by the 1st arrow. Notice that `sudo` is now listed among the groups, `tmpadmin` is a member of, as pointed to by the 2nd arrow.<br>
**View: [step4A3](screenshots/step4A3)**

Let’s run `whoami` and `pwd` to show the `adminuser` username and home directory, respectively, before renaming. First, let’s switch to the `adminuser` as pointed to by the 1st arrow to provide its password. We have successfully switched to the `adminuser` as pointed to by the 2nd arrow. Because `adminuser` is a regular user and can elevate to a `root` user/privileges, when you run `whoami` and `sudo whoami`, it will return the username `adminuser` (i.e the regular username) and `root` (i.e the `root` username) respectively as pointed to by the 3rd and 4th arrow. `pwd` will print the regular username `adminuser` working directory as pointed to by the 5th arrow.<br>
**View: [step4B1](screenshots/step4B1)**

Completely log out from the `adminuser` by running `exit` as pointed to by the 1st arrow, before switching or logging in to `tmpadmin`. Run `ls /home` to print out all the current users on the Kali Linux system as pointed to by the 2nd arrow. Let’s log in to the temporary admin we created, `tmpadmin`, as pointed to by the 3rd arrow. Notice we switch to `tmpadmin` using `su - tmpadmin` without using the `sudo` command, this is because the `tmpadmin`, unlike the `default root user` that came with Kali Linux, has a password set for it when we were creating it. After running the command, we are prompted for the `tmpadmin` password. As pointed out by the 4th and 5th arrow, run `whoami` and `sudo whoami` to view the regular and root username, which are `tmpadmin` and `root`, respectively.

The goal is to rename the underlined username `adminuser` pointed to by the 2nd arrow to `myadmin` and move/rename the home directory from `/home/adminuser` to `/home/myadmin`.<br>
**View: [step4B2](screenshots/step4B2)**

Run `sudo usermod -l myadmin adminuser` to rename the username `adminuser` to `myadmin` as pointed to by the 1st arrow. Run `sudo mv /home/adminuser /home/myadmin` to move the username adminuser's home directory `/home/adminuser` to myadmin's home directory `/home/myadmin` as pointed to by the 2nd arrow, and update the home directory Info for the user by running `sudo usermod -d /home/myadmin -m myadmin` as pointed to by the 3rd arrow.<br>
**View: [step4C1](screenshots/step4C1)**
- `-d` = new home directory.
- `-m` = move contents from the old home.

After applying all the changes, verify everything works. Run `ls /home` to list all the usernames. As pointed out by the 1st arrow, the `adminuser` has been renamed to `myadmin` as shown underlined. The command `id <username>` confirmed all the changes have been applied correctly, as shown underlined and pointed to by the 2nd arrow.<br>
**View: [step4C2](screenshots/step4C2)**

Run `exit` to log out from `tmpadmin` as pointed to by the 1st arrow, this returns us to the previous user session, which is `kali` as pointed to by the `A arrow`, since it was from `kali` we previously moved to `tmpadmin`. Log in to `myadmin` as pointed to by the 2nd arrow, the `B arrow` shows we are now logged in as `myadmin`. Run `whoami` to confirm the username `adminuser` has changed to `myadmin` and `sudo whoami` to ensure it still returns `root` as pointed to by the 3rd and 4th arrow, respectively. Run `pwd` as pointed to by the 5th arrow to confirm the home directory has also been moved/renamed to `/home/myadmin`.<br>
**View: [step4C3](screenshots/step4C3)**

## Step 5: Delete a user.
Let’s remove or delete the temporary admin user, `tmpadmin`, that we created above in `Step 4 above`.

Run `sudo deluser --remove-home <username>` to delete any specified user. The 1st arrow points to the info removing the `tmpadmin` user. Run `ls /home` to list all users, and as pointed to by the 2nd arrow, the `tmpadmin` user is no longer listed.<br>
**View: [step5A](screenshots/step5A)**

**Reboot to Apply all the Changes.**<br>
Run `sudo reboot` and wait for a few seconds to reboot the system as pointed to by the 1st arrow.<br>
**View: [step5A2](screenshots/step5A2)**

## Step 6: Lock a user.
Locking a user account in Linux (including Kali) prevents a user from logging in, without deleting the user or their files. This is useful for suspending access temporarily.

Run `sudo usermod -L <username>` to lock/disable a user account/password. This will prepend an exclamation mark ! to the password hash in /etc/shadow.

Let's view the `/etc/shadow` before locking the demouser account. We are currently operating as a `Kali` user. Run the command `sudo cat/etc/shadow` and provide the `sudo` password for `Kali`, as pointed to by the first arrow, to view hashed passwords for all users. The 2nd arrow points to the `demouser's` hashed password. Notice there is no exclamation mark `!` placed at the beginning of the hashed password in `field 2`, immediately after `kali:` in `field 1`.<br>
**View: [step6A1](screenshots/step6A1)<br>**
**View: [step6A2](screenshots/step6A2)**

Let us have a peek at a sample entry format from `/etc/shadow`<br>
`username : $id$hashed-password : lastchg : min : max : warn : inactive : expire : reserved`<br>
**View: [step6A3](screenshots/step6A3)<br>**
**View: [step6A4](screenshots/step6A4)**
|  Field                | Description                                                                                              |
|-----------------------|----------------------------------------------------------------------------------------------------------|
|  username           |The login name of the user (e.g., kali)                                                                     |
| hashed-password       |The user's hashed password.                                                                               |
| lastchg               |Days since January 1, 1970, that the password was last changed (e.g., 20432 days)                         |
| min                   | Minimum number of days between password changes (e.g., 0)                                                |
| max                   | Maximum number of days the password is valid (e.g., 99999 = 273 years = "never expire")                  |
| warn                  | Number of days before the password expires that the user gets warned (e.g., 7 days)                      |
| inactive              | Days after the password expires before the account is disabled (blank or -1 means disabled immediately)  |
| expire                | Days since Jan 1, 1970, when the account will be disabled (blank = never expires)                        |
| reserved              | Reserved for future use (usually empty)                                                                  |
<br>

**Password Field Special Cases (Field 2):**
- `$6$...`: Encrypted password (SHA-512 hash)
- `!`: Account is locked
- `*`: No password set (This cannot be used for login)
- `   `: A blank space means the password login is disabled

Run `sudo usermod -L <username>` or `sudo passwd -l <username>` to lock the `demouser`. Note that if you use the latter option, you will receive a response that the `passwd: password changed`, which means the user has been locked. After executing the command as pointed to by the 1st arrow, if successful, you will be directed to the next prompt.<br>
**View: [step6B1](screenshots/step6B1)**

After locking the user, notice the exclamation mark `!` placed at the beginning of the hashed password as pointed to by the 2nd arrow.<br>
**View: [step6B2](screenshots/step6B2)**

Because the `demouser` password has been locked, it cannot log in to the system as pointed to by the 2nd arrow.<br>
**View: [step6B3](screenshots/step6B3)**

Though the `demouser` password has been locked, its `login shell` is still operational, which is why, with a `root` user privilege as pointed to by the 1st arrow, you can `bypass` and log in to the `demouser` account. As pointed out by the 2nd arrow, we successfully log in to the `demouser` account directly without providing a password. Look at `Step 7` below on how to completely disable any user `login shell` to completely disable anyone from logging in to a particular user account.<br>
**View: [step6B4](screenshots/step6B4)**

> A `root` user can bypass and log in directly to any (both `root` and `regular`) user account, **EXCEPT** if the login shell of the user is disabled; otherwise, the `root` user will only be authenticated to provide its own `root` user password to prove it's a `root` user.

**To Unlock the User.**
Run the command `sudo usermod -U <username>` or `sudo passwd -u <username>`

Note that if you use the latter option, you will receive a response that the `passwd: password changed`, which means the user has been unlocked. If the unlocking command is executed successfully as pointed to by the 1st arrow, you will be directed to the next prompt.<br>
**View: [step6C1](screenshots/step6C1)**

As pointed out by the 2nd arrow below, the exclamation mark `!` has been removed; therefore, the `demouser` has been unlocked.<br>
**View: [step6C2](screenshots/step6C2)**

Check User Password Status.
Run the command pointed to by the 1st arrow. The option/flag -l will list the account ageing information.

Run the command sudo change -l <username> as pointed to by the 1st arrow to view a user's password status. The option/flag -l will list the account ageing information.<br>
**View: [step6D](screenshots/step6D)**

**Notes:** 
- Locking does not affect processes already running under that user. 
- If you're using key-based authentication (SSH), locking the password/user account might not stop the user from logging in — you'll need to disable their shell or revoke their SSH key too.

## Step 7: Disable a User's Shell in Kali/Linux.
Disabling a user’s shell is a smart way to block interactive logins, even if they use `SSH` with key-based authentication. This method is especially useful in hardening systems or disabling dormant accounts without deleting them.

### Option 1: Set Shell to /usr/sbin/nologin (Preferred method).
Run the command `sudo cat /etc/passwd` as pointed out by the 1st arrow to view all users. The 2nd arrow points to the `demouser` and the `demouser` login shell `bash`, is underlined.<br>
**View: [step7A1](screenshots/step7A1)<br>**
**View: [step7A2](screenshots/step7A2)**

The command `sudo usermod -s /usr/sbin/nologin <username>` will disable the `demouser` login shell as pointed to by the 1st arrow. Run the command `sudo cat /etc/passwd` to view the users list again. As pointed to by the 3rd arrow, the `demouser` login shell has changed from `/bin/bash` to `/usr/sbin/nologin`, hence `demouser` cannot use or log in to its login shell `bash`.<br>
**View: [step7B1](screenshots/step7B1)<br>**
**View: [step7B2](screenshots/step7B2)**

Because the `demouser` login shell `/usr/sbin/nologin` has been disabled, it cannot log in to the system as pointed to by the 2nd arrow.<br>
**View: [step7B3](screenshots/step7B3)**

Not even a `root` user can access or log in to the `demouser` account as pointed to by the 3rd and 4th arrow below, because the `login shell - bash` has been completely disabled.<br>
**View: [step7B4](screenshots/step7B4)**

### Option 2: Set Shell to /bin/false (Alternative method)
Run `sudo usermod -s /bin/false` as pointed by the 1st arrow to disable the login shell. If successful, you will be directed to the next prompt, where we run a command as pointed to by the 2nd arrow to view the list of all users.<br>
**View: [step7C1](screenshots/step7C1)**

The 3rd arrow in the screenshot below points to the `demouser`, and the `demouser's` login shell `/bin/bash` changed to `/bin/false`, shown underlined. After locking the `demouser`, try switching to the `demouser` as pointed to by the 4th arrow to provide the `demouser` password, you can see the log in or switching fails because we were redirected back to the `kali` user as pointed to by the 5th arrow, and **NOT** `demouser` as expected. Not even the `root` user can log in, just as demonstrated in `Option 1 above`, because the login shell has been set to false.

**To Re-enable Login.**
Run `sudo usermod -s /bin/bash <username>` to unlock the `demouser` as pointed to by the 6th arrow, and run `sudo cat /etc/passwd` to print the list of all users as pointed to by the 7th arrow to confirm it. The 8th arrow points to the `demouser` and shows that its login shell has changed from `/bin/false` to `/bin/bash`.<br>
**View: [step7C2](screenshots/step7C2)<br>**
**View: [step7C3](screenshots/step7C3)**

We can now log in or switch to the `demouser` account.

## LinkedIn Article.
- [Kali Linux Users Account Management](https://www.linkedin.com/pulse/kali-linux-users-account-management-enoch-agbu-hhzof)

## Connect with me.
[🔗 LinkedIn](https://www.linkedin.com/in/agbuenoch)<br>
[🔗 X](https://www.x.com/agbuenoch)










