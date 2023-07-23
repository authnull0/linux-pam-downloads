Download the pam_authenticator.so to your endpoint


Move the pam_authenticator.so file into /usr/local/lib/security path (if “security” folder is not available, please create it),  and also move the did.sh to / path (and provide right permissions if reqd)

Please add the below lines<EOF> to /etc/pam.d/sshd

    auth required /usr/local/lib/security/pam_gauthenticator.so debug nullok

    auth required pam_permit.so

Comment #@include common-auth - This will exclude /etc/pam.d/common-auth file

For SSH Key authentication,

Please add the below lines<EOF> to /etc/ssh/sshd_config

    AuthenticationMethods keyboard-interactive  
    KbdInteractiveAuthentication yes

Restart the ssh service

    sudo systemctl restart sshd

Now login to the VM using ssh

    ssh username@virtual-machine-name
    
AuthNull will initiate 1FA passwordless authentication

In case there are any issues in running the .so file from logs .(use below options to fix)

PAM (header )not found 

On the Centos and RHEL -build VMs, install the pam-devel package:

    sudo yum install pam-devel

On all the Debian/Ubuntu -build virtual machines, install libpam0g-dev:

    sudo apt-get install libpam0g-dev

Autoconf not installed /found 

    apt-get install autoconf automake gdb git libffi-dev zlib1g-dev libssl-dev (Debian /ubuntu)

 

Note : To ignore any user group without using google custom pam authentication, please configure the below in /etc/pam.d/sshd (The below will exclude users in group tempmfa to login without mfa google pam)

auth [success=done default=ignore] pam_succeed_if.so user ingroup tempmfa

Steps For Configuring  PAM Authenticator (Password Login) 

Please follow Step 1 as above.

For Password  authentication,

Centos Based :

Please add the below lines<EOF> to /etc/pam.d/su
    auth required /usr/local/lib/security/pam_authenticator.so debug nullok
    auth required pam_permit.so  

Comment below 

#auth           sufficient      pam_rootok.so
#auth           sufficient      pam_wheel.so trust use_uid
#auth           required        pam_wheel.so use_uid
#auth           substack        system-auth
#auth           include         postlogin
#account         required        pam_unix.so

#session                include         system-auth
#session                include         postlogin

b. Ubuntu Based 

Please add the below lines<EOF> to /etc/pam.d/common-auth

    auth required /usr/local/lib/security/pam_google_authenticator.so debug nullok
    auth required pam_permit.so  

Comment below 

#account         required        pam_unix.so

3. Please try su to any local user

    su testuser

AuthNull Authentication will initiate DID Assertion DoAuthentication Request →

Please check the logs from /var/log/auth.log file (for ubuntu based) , and /var/log/secure (centos based)

 
