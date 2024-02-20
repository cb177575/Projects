# You have been asked to provide a general security audit. 
1. First, you will review and implement aspects of the written security policy. 
2. Next, you will configure both Windows NTFS permissions and Linux standard permissions. 
3. You will use a password cracking utility to audit a user password. 
4. Finally, you will deploy certificates, including an SSL certificate for a web site and certificate management with OpenSSL.
# CompTIA Security+ Objectives: 1.2 / 3.7 / 3.8 / 3.9 / 4.1

# Review the following written security requirements:
1. Passwords will be changed every 60 days, and may not be changed more frequently than every one day. 
2. Passwords must be at least 12 characters in length. 
3. Passwords must meet complexity requirements. 
4. Users may not reuse the last 20 passwords.

# Conduct the following Configurations:
1. Edit the Default Domain Policy Group Policy Object to enforce the password and account lockout configurations. 
2. Edit the Domain Policy to enforce the remaining security options.

The Group Policy Management console will be used to browse to the corp.515support.com domain object and observe the two existing GPOs: the Default Domain Policy and the 515 Support Domain Policy.

![Group Policy](<Group Policy Management.jpg>)

3. Edit the existing Default Domain Policy to match the password and account lockout requirements defined above in the security requirements.

# Settings locations
Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies Password Policy:Password history /Max password age /Min password age /Min password length /Password must meet complexity requirements

Account Lockout Policy:
Account lockout duration
Account lockout threshold

# Edit the existing 515 Support Domain Policy to match the guest account, logon message, and last user name requirements defined above in the security requirements.

# Settings locations
Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options
Accounts: Guest account status - disabled
Interactive logon: Do not display last user name
Interactive logon: Message text for users attempting to log on
Interactive logon: Message title for users attempting to log on.
![alt text](<515 Support Domain Policy.jpg>)


1. From Administrator: Windows PowerShell run the gpupdate /force command twice.
2. From Administrator: Windows PowerShell run the gpresult /H C:\Users\Administrator\Desktop\GPreport.htm command.
3. Confirm that the .htm file exists on the Desktop
4. Close Administrator: Windows PowerShell.

# Configure Linux permissions
You will configure user access to resources in Linux. 
1. First, you need to create a user account and a group. 
2. Next, you need to create the resources (a directory and a file). 
3. Finally, you'll set ownership and permissions. Such configurations demonstrate the relationship between user identities (accounts and group memberships), ownership, and permissions.

# Create a user acccount named floyd, and then set floyd's password as Pa$$w0rd by using the adduser command.
1. adduser floyd
2. When prompted, enter and confirm Pa$$w0rd
3. Select ENTER for all other options.
4. Confirm that the floyd user account exists

# Create a group named Sales by using the groupadd command.
1. groupadd Sales
2. Confirm that the Sales group exists

# Create a directory named /SalesInfo by using the mkdir command, and then create a file in the /SalesInfo directory named SalesPolicies.txt by using the touch command.
1. mkdir /SalesInfo
2. touch /SalesInfo/SalesPolicies.txt
3. Confirm /SalesInfo directory exists.

# Grant floyd ownership of the /SalesInfo directory and the Sales group as the associated group by using the chown -R command.
1. chown -R floyd:Sales /SalesInfo
2. Confirm that floyd owns /SalesInfo

# Configure floyd with rwx, the Sales group with r-x, and all others with no access to the /SalesInfo directory and all of its contents by using the chmod -R command.

Configure Linux permissions.
chmod -R u=rwx,g=rx,o-rwx /SalesInfo


# Create an account named bobby and set Pa$$w0rd as the password by using the adduser command.
1. adduser bobby
2. When prompted, enter Pa$$w0rd
3. Accept all other defaults by pressing ENTER.

#Red Hat-derived distributions, such as the CentOS Linux on the LX1 virtual machine, typically use useradd. Debian-based Linux distributions, such as the Kali Linux on the PT1-Kali VM, prefer the adduser command to create users.

Extract the /usr/share/wordlists/rockyou.txt.gz word list file by using the gunzip command:

gunzip /usr/share/wordlists/rockyou.txt.gz

# Open the rockyou.txt wordlist file for editing
1. vim /usr/share/wordlists/rockyou.txt
2. Use the i key to enter Vim's Insert mode, and then add the password you set for Bobby above at the top of the file.
![Rockyou](Rockyou-1.jpg)

Recall that you are adding this password to the list because you believe the user has an easily-guessed password.

3. unshadow /etc/passwd /etc/shadow > crack-this-file

4. Run the following command to crack passwords:
john --wordlist=/usr/share/wordlists/rockyou.txt crack-this-file

5. Redirect the results of the john --show crack-this-file to a text file:
john --show crack-this-file > password-audit.txt
6. Display the password-audit.txt file contents by using the cat command.
![alt text](<Password File.jpg>)

## Request a Server Certificate
# Open the Internet Information Services (IIS) Manager console.
Select the Server Certificates applet.

In the Actions pane, select Create Domain Certificate. Complete the Create Certificate wizard:

Common Name: updates.corp.515support.com

Organization: 515 Support

Organizational Unit: Web Team

Fill in City, State, and other fields with your information or just use Cloud

Online Certification Authority: Select 515support-CA

Friendly name: updates.corp.515support.com Domain-issued Certificate

After a few seconds, the certificate request will be granted.

Right-click Default Web Site and select Edit Bindings.

Add a site binding for https that uses a hostname of updates.corp.515support.com and binds the updates.corp.515support.com Domain-issued certificate SSL certificate.

Remove the http entry.

Close the dialog box, and return to the main IIS console.

In Internet Explorer attempt to connect to https://updates.corp.515support.com. If prompted, select OK to acknowledge the warning.

It may take up to one minute for the connection to be established.

You should see the 515 Support User Portal web page.

Close the browser.
![web page](<515 Web Portal.jpg>)
Select the PT1-Kali VM. If necessary, sign on as root with Pa$$w0rd as the password.

From the top bar, open the Linux Terminal Emulator icon.

You must start in the /root folder. Run cd if you are not sure.

1. Create a directory named keys in the root user's home directory, and then use the cd command to change to the keys directory.

2. Generate a private key and extract public to prepare to create a certificate signing request (which occurs below). Type the following command, then press ENTER:

openssl genrsa -out corp.515support.com.key 2048

3. Confirm that the key file exists.

4. Extract the public key.

openssl rsa -in corp.515support.com.key -pubout -out corp.515support.com_public.key

5. Use the ls command to display the public key file.

6. Display the public key file:

cat corp.515support.com_public.key

7. Confirm that the public.key file exists.

8. Generate a certificate signing request. Type the following command, then press ENTER:

openssl req -new -key corp.515support.com.key -out corp.515support.com.csr
Provide the following answers to the prompts:

Country Code: your country
State or Province Name: your state or province
Locality Name: your city
Organization Name: 515 Support
Organizational Unit Name: WebServices
Common Name: your name
Email Address: admin@515support.com
When prompted to enter a challenge password and an optional company name, press ENTER.

This OpenSSL command generates a certificate signing request on behalf of the Apache web server service for a web site.

Run the ls command to display the .csr file.


9. Confirm that the .csr file exists
