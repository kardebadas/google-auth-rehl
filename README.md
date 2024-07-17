
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

```bash
sudo chown USERNAME:USERNAME /home/USERNAME/.google_authenticator
sudo chmod 600 /home/USERNAME/.google_authenticator
sudo restorecon -R -v /home/USERNAME
```

**Note:** Replace `USERNAME` with the actual username you are configuring.

---

Following these steps will install and configure Google Authenticator for SSH on your system.
