# Hello, COSMOS

COSMOS is a wireless research platform for experiments involving advanced wireless technologies. In this tutorial, you will prepare your workstation, create a COSMOS account, and add an SSH key to your account.

>[!NOTE]
>Before you start, ask your instructor or research advisor for the name of the COSMOS group you should join. You will need to select this group when you register.


## Prepare your workstation 

To use COSMOS, you'll need to prepare your workstation (the laptop or PC you are going to use for your experiments) with a terminal application. You will use SSH from the terminal to connect to your COSMOS resources.

You may use the built-in terminal on Linux or Mac. On Windows 10 or 11, you may use PowerShell, Windows Terminal, or any other terminal application that has an SSH client.

## Create an account on COSMOS

#### Create your COSMOS account

First, you will request an account on the COSMOS portal.

1. Open the [COSMOS user registration form](https://www.cosmos-lab.org/register_usr).
2. Select the group name that your instructor or research advisor gave you.
3. Enter your contact information and choose your mailing list preference.
4. Submit the form.

>[!IMPORTANT]
>Once you fill in the form, you will receive an email to confirm your Account Request. You must open the link in that email within 30 minutes to submit the account creation request. If you do not receive the email, check your spam folder and allow messages from `accountmanager@orbit-lab.org`.

After you confirm your request, the PI for your group must approve it. COSMOS will send you another email when the PI approves your account. You can then log in to the [COSMOS portal](https://www.cosmos-lab.org/portal/).

#### Generate SSH keys

Next, you will generate an SSH key pair. You will add the public key to your COSMOS account, and then you will use these keys when connecting to COSMOS resources.

> Note: If you already have an SSH key pair, you can use it with COSMOS - copy the contents of the public key, then skip to the "Upload your public key to COSMOS" section and continue there. If you don’t already have an SSH key pair, continue with the rest of this section.

SSH public-key authentication uses a pair of separate keys (i.e., a key pair): one “private” key, which you keep a secret, and the other “public”. A key pair has a special property: any message that is encrypted with your private key can only be decrypted with your public key, and any message that is encrypted with your public key can only be decrypted with your private key. 

This property can be exploited for authenticating login to a remote machine. First, you upload the public key to a special location on the remote machine. Then, when you want to log in to the machine: 

* You use a special argument with your SSH command to let your SSH application know that you are going to use a key, and the location of your private key. If the private key is protected by a passphrase, you may be prompted to enter the passphrase (this is not a password for the remote machine, though).
* The machine you are logging in to will ask your SSH client to “prove” that it owns the (secret) private key that matches an authorized public key. To do this, the machine will send a random message to you.
* Your SSH client will encrypt the random message with the private key and send it back to the remote machine.
* The remote machine will decrypt the message with your public key. If the decrypted message matches the message it sent you, it has “proof” that you are in possession of the private key for that key pair, and will grant you access (without using an account password on the remote machine.)

(Of course, this relies on you keeping your private key a secret.)

We’re going to generate a key pair on our laptop, then upload it to our COSMOS profile.

Open a terminal, and generate a key named `id_ed25519_cosmos`:

```
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_cosmos
```

Follow the prompts to generate and save the key pair. The output should look something like this: 

```
$ ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_cosmos
Generating public/private ed25519 key pair.
Enter file in which to save the key (/users/ffund01/.ssh/id_ed25519_cosmos):
Enter passphrase (empty for no passphrase):
Enter same passphrase again: 
Your identification has been saved in /users/ffund01/.ssh/id_ed25519_cosmos.
Your public key has been saved in /users/ffund01/.ssh/id_ed25519_cosmos.pub.
The key fingerprint is:
SHA256:rYsVy23PwinHQ8GMCbEzy52KTssKnT/RuoMwUt2j8D8 ffund@example.com
The key's randomart image is:
+--[ED25519 256]--+
|      ..         |
|      ..         |
|   . .+. =       |
|  o ..o=oo+      |
| . o oo.S ..     |
|+. .+..o =.      |
|ooo.o+. =+o.     |
| ..=+.Eo.oBo     |
|  ..*+...o oo    |
+----[SHA256]-----+
```

If you use a passphrase, make a note of it somewhere safe! (You don’t have to use a passphrase, though - feel free to leave that empty for no passphrase.)

You will need the contents of your public key in the next step. To see your public key from the terminal, run

```
cat ~/.ssh/id_ed25519_cosmos.pub
```

The output will start with `ssh-ed25519`, e.g.

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKsJ8MzC+ml/yEWbCsJJOqUENXumlhE+CYOvDkSI/Kk1 ffund@example.com
```

This text string is your public key. You can copy it from the terminal output.


#### Upload your public key to COSMOS

Once you are logged in to the [COSMOS portal](https://www.cosmos-lab.org/portal/),

1. Click your name in the upper-right corner, then select "SSH Keys".
2. Paste the entire contents of `~/.ssh/id_ed25519_cosmos.pub` into the public key field. Make sure that you paste the public key, not the private key.
3. Click "Add Key".
4. Confirm that your key appears in the list of uploaded keys.
