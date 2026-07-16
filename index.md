# Hello, COSMOS

COSMOS is a wireless research platform for experiments involving advanced wireless technologies. In this tutorial, you will prepare your workstation, create a COSMOS account, and add an SSH key to your account. Then, you'll run a simple COSMOS experiment in which you set up a WiFi access point and associate a client to it.

> Before you start, ask your instructor or research advisor for the name of the COSMOS group you should join. You will need to select this group when you register.


## Prepare your workstation 

To use COSMOS, you'll need to prepare your workstation (the laptop or PC you are going to use for your experiments) with a terminal application. You will use SSH from the terminal to connect to your COSMOS resources.

You may use the built-in terminal on Linux or Mac. On Windows 10 or 11, you may use PowerShell, Windows Terminal, or any other terminal application that has an SSH client.


#### Generate SSH keys

Next, you will generate an SSH key pair. Later, you will add the public key to your COSMOS account, and then you will use these keys when connecting to COSMOS resources.

> Note: If you already have an SSH key pair, you can use it with COSMOS - copy the contents of the public key, then skip to the "Create an account on COSMOS" section and continue there. If you don't already have an SSH key pair, continue with the rest of this section.

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


## Create an account on COSMOS

Now, you are ready to set up your COSMOS account!

#### Create your COSMOS account

First, you will request an account on the COSMOS portal.

1. Open the [COSMOS user registration form](https://www.cosmos-lab.org/register_usr).
2. For "Organization", select the group name that your instructor or research advisor gave you.
3. Enter your contact information and choose your mailing list preference.
4. Submit the form.
5. Check your email for a confirmation link. Once you fill in the form, you will receive an email to confirm your Account Request. You must open the link in that email **within 30 minutes** to submit the account creation request. If you do not receive the email, check your spam folder.

After you confirm your request, the PI for your group must approve it. COSMOS will send you another email when the PI approves your account. You can then log in to the [COSMOS portal](https://www.cosmos-lab.org/portal/).

#### Upload your public key to COSMOS

Once you are logged in to the [COSMOS portal](https://www.cosmos-lab.org/portal/),

1. Click your name in the upper-right corner, then select "SSH Keys".
2. Paste the entire contents of `~/.ssh/id_ed25519_cosmos.pub` into the public key field. Make sure that you paste the public key, not the private key.
3. Click "Add Key".
4. Confirm that your key appears in the list of uploaded keys.


Now, you can test your key! Use the following SSH command, replacing the username with your own and the example identity filename with *your* COSMOS private key:

```
ssh -i ~/.ssh/id_ed25519_cosmos YOUR_USERNAME@gw.cosmos-lab.org
```
The first time you connect, SSH may ask you to confirm the host key. Type `yes`, and press Enter. 

A successful connection will open a shell on the COSMOS gateway. Run 

```
exit
```

in this shell to return to your local terminal.

## Start an experiment

Now, you are ready to run an experiment on COSMOS!

COSMOS resources are organized into "domains". Each domain is an isolated environment with its own console, nodes, and control network. COSMOS has several kinds of domains:

* **Top-tier testbeds** support large-scale experiments. These include the outdoor `bed` domain in West Harlem and the indoor `grid` domain at WINLAB. We will not use the grid for small experiments; it is reserved for experiments that need its large-scale deployment.
* **Sandboxes** are small, isolated environments, typically with two nodes and a console. Each sandbox has different radios, compute resources, and other devices. Sandboxes are intended for developing and debugging an experiment before you scale it to a larger testbed, or for small-scale experiments (like the one we will run!)
* **Specialized domains** have special capabilities, for example, to represent a smart city intersection or a manufacturing setting.

You can read more about these environments in the [COSMOS domains guide](https://www.cosmos-lab.org/en/wiki/public/hardware/domains).

Since every domain has slightly different hardware (e.g. wireless radio types), the first step in any experiment is to identify where it should run. In this "Hello, COSMOS" experiment, we will need two nodes to run a basic WiFi experiment, so we must start by using the COSMOS Hardware Search to find the sandboxes that have two WiFi nodes!

#### Find a sandbox with WiFi

Log in to the [COSMOS portal](https://www.cosmos-lab.org/portal/), then:

1. Open "Status" in the navigation menu and select "Hardware Search".
2. Under "Wireless / IoT", select "WiFi".
3. Expand the "Domain" filter.
4. Select each sandbox domain: `sb1`, `sb4`, `sb5`, `sb6`, and `sb7`. Leave the top-tier testbeds (`grid`, `bed`), and the specialized domains (e.g., `weeks`) unchecked.

![](images/hardware-search-wifi-sandboxes.png)

The filtered results show WiFi nodes the selected domains. For this experiment, we can see that **`sb5`, `sb6`, or `sb7`** are NJ sandboxes with two WiFi-capable nodes, so we could use any of these for our experiment.


### Reserve a sandbox

COSMOS resources are available by reservation. Each student should create an individual two-hour reservation on either `sb5`, `sb6`, or `sb7`, and then run the experiment at the reserved time.

Open the [COSMOS Scheduler](https://www.cosmos-lab.org/portal/scheduler) (choose the "Scheduler" tab in the portal). The scheduler shows one day at a time, but you can use the date field in the upper-right corner to move to the day when you plan to run the experiment. The scheduler uses the COSMOS time zone, which is shown above the calendar.

![](images/reservation-scheduler.png)

Find the row for `sb5.cosmos-lab.org`, `sb6.cosmos-lab.org`, or `sb7.cosmos-lab.org`. Choose a two-hour period in which that domain is available, then click the grid square at the start of that period. For example, to reserve 2:00-4:00 PM, click the 2:00 PM square.

The "New Reservation" form will open:

![](images/reservation-form.png)

1. Confirm that "Resource" shows the sandbox you intended to reserve.
2. Set the start and end date to the same day.
3. Set the end time two hours after the start time. COSMOS limits each reservation request to 120 minutes.
4. For "Summary", enter `Hello COSMOS WiFi experiment`.
5. Leave "Repeat" set to "Does not repeat".
6. Leave "Invite users" empty and leave "Group reservation" unchecked. This is an individual reservation.
7. Click "Create".

After you create the reservation, it appears in yellow while it is pending approval. An approved reservation appears in dark blue (other users' reservations) or purple (your own).

COSMOS uses a two-stage automatic approval process. Requests submitted before noon for the following day receive pre-approval that day. Requests submitted less than 12 hours before their start time receive just-in-time approval at the beginning of the reserved time. You'll receive an email when your reservation is approved.

Users may request a time slot even if another reservation is pending for that slot. The scheduler marks overlapping requests as a conflict, and decides which request to approve. COSMOS gives preference to users who have consumed less testbed time during the previous two weeks, so you should not reserve extra slots that you do not expect to use. Using or requesting more testbed time can reduce your chance of approval when your request conflicts with another user's request.

## Access your sandbox

Wait until your reservation is approved and its start time has arrived. You cannot access the sandbox console before then.

You can confirm that your reservation is active by visiting the "Status > Testbed Status" page in the portal. If your reservation is active, it will show the status of the testbed that you reserved.

Once your reservation is active, open a terminal on your workstation and SSH to the console for your reserved domain. In the following command, replace `YOUR_USERNAME` with your COSMOS username and replace the `X` in `sbX` with `5`, `6`, or `7`, depending on which sandbox you reserved:

```bash
# runs on your workstation
ssh -i ~/.ssh/id_ed25519_cosmos YOUR_USERNAME@sbX.cosmos-lab.org
```

The console is a shared control machine for the domain. You will use it to image, turn on, and access the two experiment nodes in the sandbox.

#### Check node status

Run:

```bash
# runs on console
omf stat -t all
```

You should see two nodes. Their initial power state may differ from this example:

```console
$ omf stat -t all
-----------------------------------------------
 Available Nodes (2):
   node1-1.sb6.cosmos-lab.org       POWERON
   node1-2.sb6.cosmos-lab.org       POWERON
-----------------------------------------------
```

### Load a disk image

Load the `ubuntu2404-uhd4.8-gr3.10.ndz` image onto both nodes:

```bash
# runs on console
omf load -i ubuntu2404-uhd4.8-gr3.10.ndz -t node1-1,node1-2
```

Imaging takes several minutes. `omf load` resets the nodes, writes the image, and powers the nodes off when it finishes. A successful run ends with output like this:

```console
Loading disk image onto 2 nodes
   Domain:  sb6.cosmos-lab.org
   Image:   ubuntu2404-uhd4.8-gr3.10.ndz
   Disk:    /dev/sda
   Boot reset timeout: 150s
   Resize:  20 GB (default)
   Disk loading timeout: 800s

Resetting nodes...
Booted 2/2 nodes
Loading image 'ubuntu2404-uhd4.8-gr3.10.ndz' via 224.0.0.5:7170...
Powering off all nodes...

 -----------------------------
 Imaging Process Done
 2 nodes successfully imaged
 -----------------------------
```

Do not interrupt the command while it is writing the image.

### Turn on the nodes

Turn on both nodes:

```bash
# runs on console
omf tell -a on -t node1-1,node1-2
```

Each node should reply `OK`:

```console
-----------------------------------------------
 Node: node1-1.sb6.cosmos-lab.org       Reply: OK
 Node: node1-2.sb6.cosmos-lab.org       Reply: OK
-----------------------------------------------
```

Wait about one minute for the nodes to boot, then check their state:

```bash
# runs on console
omf stat -t all
```

Both nodes should report `POWERON`.

## Configure the WiFi network

You will configure `node1-1` as a WiFi access point named `hello-cosmos` and connect `node1-2` as a client. The two nodes will use static addresses on an isolated `192.168.50.0/24` network.

Open two more terminals on your workstation and SSH to the sandbox console in each. From the first console session, connect to `node1-1`:

```bash
# runs on console
ssh root@node1-1
```

From the second console session, connect to `node1-2`:

```bash
# runs on console
ssh root@node1-2
```

You are now working as `root` on each experiment node. Keep the two terminals separate and check the shell prompt before you run each command.

### Install the WiFi tools

Run these commands on **both nodes**:

```bash
# runs on node1-1 and node1-2
apt update
apt -y install hostapd wpasupplicant iperf3 rfkill
modprobe ath9k
rfkill unblock wifi
```

Confirm that the Atheros WiFi interface is available:

```bash
# runs on node1-1 and node1-2
iw dev
```

Look for the interface listed under `phy#0`. In the example below it is named `wlp3s0`, but the name can differ across hardware or disk images:

```console
phy#0
        Interface wlp3s0
                addr 00:15:6d:84:fb:7a
                type managed
```

### Start the access point on node1-1

In the `node1-1` terminal, create the access point configuration:

> If `iw dev` showed an interface name other than `wlp3s0`, replace `wlp3s0` with that name in the configuration and commands below.

```bash
# runs on node1-1
cat > /tmp/hostapd.conf <<'EOF'
interface=wlp3s0
driver=nl80211
ssid=hello-cosmos
hw_mode=g
channel=11
EOF
```

This tutorial uses an open (no password, no encryption) network inside your isolated sandbox. In general, you would not use an open access point for a production network!

Assign the AP its address and start `hostapd`:

```bash
# runs on node1-1
ip link set wlp3s0 down
ip addr flush dev wlp3s0
ip addr add 192.168.50.1/24 dev wlp3s0
ip link set wlp3s0 up
hostapd -B /tmp/hostapd.conf
```

Confirm the AP state:

```bash
# runs on node1-1
iw dev wlp3s0 info
```

The output should identify the SSID, AP mode, and channel:

```console
Interface wlp3s0
        addr 00:15:6d:84:fb:7a
        ssid hello-cosmos
        type AP
        channel 11 (2462 MHz), width: 20 MHz, center1: 2462 MHz
        txpower 21.00 dBm
```

### Associate node1-2 with the AP

In the `node1-2` terminal, create the client configuration:

> If `iw dev` shows an interface name other than `wlp3s0`, replace `wlp3s0` with that name in the commands below.

```bash
# runs on node1-2
cat > /tmp/wpa_supplicant.conf <<'EOF'
ctrl_interface=/run/wpa_supplicant
network={
    ssid="hello-cosmos"
    key_mgmt=NONE
}
EOF
```

Start the client:

```bash
# runs on node1-2
ip link set wlp3s0 down
ip addr flush dev wlp3s0
ip link set wlp3s0 up
wpa_supplicant -B -i wlp3s0 -c /tmp/wpa_supplicant.conf
```

`wpa_supplicant` now runs in the background. Check its status:

```bash
# runs on node1-2
wpa_cli -i wlp3s0 status
```

The association is ready when the output includes `ssid=hello-cosmos` and `wpa_state=COMPLETED`:

```console
bssid=00:15:6d:84:fb:7a
freq=2462
ssid=hello-cosmos
id=0
mode=station
pairwise_cipher=NONE
group_cipher=NONE
key_mgmt=NONE
wpa_state=COMPLETED
address=00:15:6d:84:fb:6f
```

If the state is `SCANNING` or `ASSOCIATING`, run the status command again.

Once the state is `COMPLETED`, assign the client address:

```bash
# runs on node1-2
ip addr add 192.168.50.2/24 dev wlp3s0
```

Then check the association:

```bash
# runs on node1-2
iw dev wlp3s0 link
```

A successful association shows the AP address and SSID:

```console
Connected to 00:15:6d:84:fb:7a (on wlp3s0)
        SSID: hello-cosmos
        freq: 2462.0
        signal: -41 dBm
        tx bitrate: 130.0 MBit/s MCS 15
```

The MAC address, signal level, and bitrate in your output may differ.

## Test the WiFi link

First, send four ICMP echo requests from `node1-2` to the AP:

```bash
# runs on node1-2
ping -c 4 192.168.50.1
```

A working link returns four replies with no packet loss:

```console
PING 192.168.50.1 (192.168.50.1) 56(84) bytes of data.
64 bytes from 192.168.50.1: icmp_seq=1 ttl=64 time=7.92 ms
64 bytes from 192.168.50.1: icmp_seq=2 ttl=64 time=0.885 ms
64 bytes from 192.168.50.1: icmp_seq=3 ttl=64 time=0.852 ms
64 bytes from 192.168.50.1: icmp_seq=4 ttl=64 time=0.918 ms

--- 192.168.50.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss
```

The ping test confirms that the two nodes can exchange IP packets over the WiFi network.

### Measure throughput

In the `node1-1` terminal, start an `iperf3` server:

```bash
# runs on node1-1
iperf3 -s
```

Leave it running. In the `node1-2` terminal, send traffic over the WiFi link for five seconds:

```bash
# runs on node1-2
iperf3 -c 192.168.50.1 -t 5
```

The test run produced this summary:

```console
Connecting to host 192.168.50.1, port 5201
[  5] local 192.168.50.2 port 53514 connected to 192.168.50.1 port 5201
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-5.00   sec  53.6 MBytes  89.9 Mbits/sec    0  sender
[  5]   0.00-5.01   sec  51.4 MBytes  86.1 Mbits/sec       receiver

iperf Done.
```

Your throughput will vary with radio conditions. A completed test with nonzero throughput confirms that application data crossed the WiFi link.
