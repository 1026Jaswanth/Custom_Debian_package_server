# Custom_Debian_package_server
hosting debian package server in RPI 
Sever RPI
Step 1: Set Up the Raspberry Pi as a Web Server
```
sudo apt update
sudo apt install apache2
```
```
sudo systemctl start apache2
sudo systemctl enable apache2
```
Step 2: Create a Directory for Your Packages
`sudo mkdir -p /var/www/html/repo`
```
sudo chown -R www-data:www-data /var/www/html/repo
sudo chmod -R 755 /var/www/html/repo
```
Step 3: Create a Custom Debian Package
```
mkdir -p mypackage/DEBIAN
mkdir -p mypackage/usr/local/bin
```
Place your FOTA files in the usr/local/bin directory
`nano ~/mypackage/DEBIAN/control`
```
Package: my-fota-package
Version: 1.0
Section: base
Priority: optional
Architecture: all
Maintainer: Your Name <you@example.com>
Description: Custom FOTA package for STM32 MCU
```
Ensure proper persmissions
`chmod 644 ~/Eruvakapackages/DEBIAN/control`

Build the Debian Package
`dpkg-deb --build mypackage`

Step 4: Add the Package to the Repository
Copy the Package to the Repository Directory: Move the .deb file to the web server directory.
`sudo cp mypackage.deb /var/www/html/repo/`

Create Package Metadata: Use dpkg-scanpackages to generate the Packages.gz file.
```
cd /var/www/html/repo
sudo apt install dpkg-dev  # Install if not already installed
dpkg-scanpackages . /dev/null | gzip -9c > Packages.gz
```
If any issues check the ownership and change to pi pi:
`sudo chown pi:pi /var/www/html/repo`

Step 5: Configure the Web Server to Serve the Repository
Test Accessing the Repository: In your web browser, navigate to `http://<your_rpi_ip>/repo/.`
You should see your mypackage.deb file listed.
 
# **Client RPI:**
Download the package
`wget http://192.168.71.244/repo/Eruvakapackages.deb`

```
mkdir extracted
dpkg-deb -x Eruvakapackages.deb extracted/
cp extracted/usr/local/bin/* /home/pi/Fotabins/
 ```

Multiple packages:

 
2. Create Control Files
In each DEBIAN/ directory, create a control file specific to that package. For example, for package1:
plaintext
Copy code
```
Package: binary1-package
Version: 1.0
Section: base
Priority: optional
Architecture: all
Maintainer: Your Name <you@example.com>
Description: Custom package for binary1
```
And for package2, create a similar control file:
plaintext
Copy code
```
Package: binary2-package
Version: 1.0
Section: base
Priority: optional
Architecture: all
Maintainer: Your Name <you@example.com>
Description: Custom package for binary2
```
4. Build Each Package
Navigate to each package directory and build them separately:
bash
Copy code
## For package1
```
cd /home/pi/EruvakaFota/package1
dpkg-deb --build .
```
## For package2
```
cd /home/pi/EruvakaFota/package2
dpkg-deb --build .
```

This will create package1.deb and package2.deb in their respective directories.
4. Update the Repository
1.	Copy the .deb Files: Move both .deb files to your repository directory:
bash
Copy code
```
sudo cp /home/pi/EruvakaFota/package1.deb /var/www/html/repo/
sudo cp /home/pi/EruvakaFota/package2.deb /var/www/html/repo/
```
3.	Update the Packages File:
bash
Copy code
```
cd /var/www/html/repo
dpkg-scanpackages . /dev/null | gzip -9c > Packages.gz
```
5. Accessing the Packages from Clients
On the client RPIs, you can add the same repository and install the specific packages using:
bash
Copy code
```
sudo apt update
sudo apt install binary1-package  # For the first binary
sudo apt install binary2-package  # For the second binary
```

Server Debugging:
`ls -l /var/www/html/repo/latest/`
It displays the read and write permissions
It should display similar to like this 
 

`zcat /var/www/html/repo/latest/Packages.gz`

It fetches the package information which is inside the DEBIAN/control
# client  checks:
`sudo apt-get update --allow-insecure-repositories`
Signig the deb files :
If
Updated the server with control version to 1.1:
`gpg --armor --detach-sign Packages.gz`
This will generate a Packages.gz file containing the list of available packages and a detached signature file (Packages.gz.sig).
To verify in client device :
New format with release
 
