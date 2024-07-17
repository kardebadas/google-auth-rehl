
# Google Authentication Installation on CentOS/REHL 9

## Step 1: Install Google Authenticator

Install the necessary packages:

```bash
sudo dnf install epel-release -y
sudo dnf install google-authenticator -y
```

## Step 2: Configure SSH

### Modify `/etc/ssh/sshd_config.d/02-redhat.conf`

Add the following lines to the configuration file:

```conf
SyslogFacility AUTHPRIV

GSSAPIAuthentication yes
GSSAPICleanupCredentials no

UsePAM yes

ChallengeResponseAuthentication yes
PasswordAuthentication yes
PubkeyAuthentication yes
KbdInteractiveAuthentication yes
```

### Modify `/etc/pam.d/sshd`

Add the following line after the `auth` configuration:

```conf
auth       required     pam_google_authenticator.so nullok debug [authtok_prompt=Enter your secret token: ]
```

## Step 3: Generate Google Authenticator Configuration

Run the following command as the user you want to configure Google Authenticator for:

```bash
sudo -u USERNAME google-authenticator
```

## Step 4: Set Permissions and SELinux Contexts

Ensure the permissions and SELinux contexts are correct:

# SELinux Policy Module for SSHD

This guide explains how to create and apply an SELinux policy module to allow `sshd` to perform necessary file operations in the user's home directory.

## Steps

1. **Create the policy file**:

    ```bash
    sudo nano /tmp/local.te
    ```

2. **Add the following content to `local.te`**:

    ```plaintext
    module local 1.0;

    require {
        type user_home_dir_t;
        type sshd_t;
        class file { create open read write setattr unlink rename getattr };
    }

    #============= sshd_t ==============
    allow sshd_t user_home_dir_t:file { create open read write setattr unlink rename getattr };
    ```

3. **Compile and install the module**:

    ```bash
    checkmodule -M -m -o /tmp/local.mod /tmp/local.te
    semodule_package -o /tmp/local.pp -m /tmp/local.mod
    sudo semodule -i /tmp/local.pp
    ```

This will create and load the SELinux policy module, allowing `sshd` to create, open, read, write, set attributes, unlink, and rename files in the user's home directory.

---

Following these steps will install and configure Google Authenticator for SSH on your system.
