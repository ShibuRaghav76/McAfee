# Steps for Hardening

## MAC Hardening 

1. Create admin user.
2. Set Password. `Password should be split and owned by leads (2 members in the team)`.
3. Connect to uob wifi (public wifi).
4. Install home brew `(execute command /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)")`.
5. Create standard user with given LAN ID.
6. Copy hardening scripts from usb to local desktop `(found under https://bitbucket.sg.uobnet.com/projects/MAC/repos/osx-l1/browse/)`.
7. Place hardening scripts in downloads folder.
8. Run `sudo ./runH.sh with parameter username and password`.
9. Open Key Chain and put the root and intermediate certs in `Keychain -> System -> Certificates`
10. Trust both certs.
11. Logout from admin user.
12. Login with the user.

## Sign Certificate by UOB

13. Open Keychain and generate the CSR(Request a certificate from a certificate authority)
14. Enter email as `SGMUOB<last 7 chars of MAC serial number>@uobgroup.com`
15. Common Name: `SGMUOB<7characters>.sg.uobnet.com` 
16. Save in desktop and rename it to `SGMUOB<laast 7 characters>.csr`
17. Consolidate the CSR and raise CRQ to ISTOA team for CSR signing. 
18. Copy signed cert on laptop and install/trust cert.

## Connect to WiFi Network

19. Join other `Network -> Network Name: EWLmacdev, Security: WPA2 Enterprise -> select cert`
20. Open `System Preference -> Network -> Advanced -> DNS`
21. Put the following four IP addresses:

		172.18.3.40
        172.28.3.40
		172.29.24.181
		172.29.227.88
		172.29.22.41



## McAfee Endpoint Security Hardening Instructions

#### Note: If McAfee Endpoint Security is installed. Then follow the steps to uninstall McAfee Endpoint Security. 
#### Please ensure all machine are named as follow `SGM[Serial# of the MAC]`


22. Uninstall McAfee DLP and ENS
    	
	a. Execute the following commands from terminal
	
		sudo /usr/local/McAfee/uninstall DLP (enter Admin password when prompted)
		sudo /usr/local/McAfee/uninstall EPM (enter Admin password when prompted)
		sudo /Library/McAfee/cma/scripts/uninstall.sh (enter Admin password when prompted)

	b. Verify McAfee Agent and ENS is not running by executing the following commands - 
	
		ps -ef |grep start

			- We should see that there are no processes.

	c. Restart laptop


23. Install telnet by executing the following command -

		brew install telnet


24. Execute following command 
	
		telnet 172.29.14.203 443

#### Proceed to next step only if you are able to telnet successfully.

25. Click on the Forcepoint icon at the top right hand corner and verify the connection status is CONNECTED

26. Install McAfee Agent and ENS

	a. Download [NTEPPUSGV12_Agent_MAC.zip](https://bitbucket.sg.uobnet.com/projects/MAC/repos/osx-l1/browse/McAfee/NTEPPUSGV12_Agent_MAC.zip)

	b. Extract `install.sh` from `NTEPPUSGV12_Agent_MAC.zip`
	
	c. Execute the following command from terminal from the location where `install.sh` is extracted

		Ensure that you are connected via LAN when you execute the following command.

		sudo ./install.sh -i

	d. Once done, download [McAfee-Endpoint-Security-for-Mac](https://bitbucket.sg.uobnet.com/projects/MAC/repos/osx-l1/browse/McAfee/McAfee-Endpoint-Security-for-Mac-10.5.9-RTW-standalone-109.dmg).
	
	e. Install McAfee by double clicking on `McAfee-Endpoint-Security-for-Mac-10.5.9-RTW-standalone-109.dmg` mount the device.
	
	f. Double click on the Package file and proceed with the installation.
	
	g. At the component selection prompt; uncheck McAfee Firewall and McAfee Web Content protection.
	
	h. Continue and complete the installation
	
	i. Restart laptop.

27. Verify McAfee Agent and ENS is up and running by executing the following commands - 
	
		ps -ef | grep start

			- We should see that the following processes are up and running -
				
				macmnsvc 
				masvc

		Ensure that you are connected via LAN when you execute the following command.
		
		/Library/McAfee/agent/bin/cmdagent -p

			- This will force the agent to send props to server.

		/Library/McAfee/agent/bin/cmdagent -c

			- To force a refresh.

		/Library/McAfee/agent/bin/cmdagent -i

			- We should see that `LastASCTime` and `LastPolicyUpdateTime` are not blank
 

	Note: See attached for more info. [Verification Screenshot](https://bitbucket.sg.uobnet.com/projects/MAC/repos/osx-l1/browse/McAfee/verify.jpg).

## Lynis (for Audit) - Experimental

1. Install Lynis by executing the following command -

		brew install lynis

2. Audit the laptop by executing the following command - 

		lynis audit system | tee audit.log


## Manual Installation of Unified Agent on Mac

1. Make sure you are connected with `EWLmacdev with HTTP/HTTPS proxy (proxy.sg.uobnet.com and port 8080)` enabled with authentication details `LanID (unc…).` Open browser and access google.com and on authentication popup enter LanId/pwd. Once done, please proceed with installation

2. Run the following command on terminal
`sudo defaults write com.bluecoat.ua cmurl https://proxy.sg.uobnet.com:8088`

3. Mount the .dmg installer and double click the .pkg to install Unified Agent

4. After successful installation, you will see a BC icon on top of your task/menu bar.

5. Click on the BC Icon and select Status, then Advance. Select “Check for configuration update” and it will poll for config from the proxysg

`You can change the Client Manager Address on the first setup, once connected to the proxy you will not be able to change it anymore. Reinstalling if you want to change the Client Manager Address`

6. Reboot your macbook if you see the following error
`This action can't be performed because Unified Agent daemon is not currently running`

7. Once you have poll the config, try to access sites that are block and you will be shown notification that the service are blocked on the macbook top right hand corner

8. To uninstall Unified Agent, please run the following command via terminal

9. open "/Library/Application Support/bcua/Uninstall Blue Coat Unified Agent.app" 

or

sudo "/Library/Application Support/bcua/Uninstall Blue Coat Unified Agent.app/Contents/MacOS/uninstall‐helper"


10. Below is expected result in ewlmacdev:

`File Status: Disabled due to Location`

11. Below result for other wifi:

`File Status: Running`



## Troubleshooting 


### Network Issues (Developers sometimes cannot connect to network)

1. Please wait 1 or 2 hour to connect again.
2. If it cannot, raise again certificate to ISTOA or reach out to leads.


### Passwords expiring

If the OS tells your password is expiring:

1. Don't create a new passoword.
2. Go to the admin user and request to login as admin.
3. In the terminal, run:
	- `dscl . delete /Users/{your user} accountPolicyData`
	- `dscl . passwd /Users/{your user} {your old password}`

    
    
