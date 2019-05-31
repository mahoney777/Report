# Report – &quot;Beagle Bone Backdoor&quot; by Samuel Mahoney

The Beagle Bone Backdoor is a system which would allow an attacker who has physical access to a network to gain a permanent reverse shell. This would allow the attacker to access the network from a remote location after they have gained physical access. By doing this the attack has a physical staging area for Reconnaissance, scanning and exploitation. A backdoor, in the world of infosec, is a means to access a computer system or network which bypasses the in-place security mechanisms. Not all backdoors are used for malicious purposes, for example, a backdoor might be installed so that the network or computer can be accessed for remote administration or troubleshooting purposes. However, in most cases a backdoor is used as a way for an attacker to maintain access and is then used to spread a worm or virus or continue to gather information.

My task was to create the code for the reverse shell between the c2 server and the clients. I decided to program it in python since it is my strongest programming language. I created both a server and client script which takes advantage of the paramiko and socket modules in python. Paramiko is a means of working with the SSHv2 protocol in python3 that provides secure connections between remote machines.

![Reverse Shell Example](https://github.coventry.ac.uk/mahone10/Report/blob/master/Github/report.png)
In a normal SSH shell the client is sending commands to the SSH server which is normally set up on a machine which requires remote access. In a reverse SSH connection the server sends the commands to the client. This is important because it is an effective way to bypass firewall since most networks allow incoming connections (This is normally what the client would do). When the server connects to the client it opens a tunnel which allows the server to send commands to the client though the tunnel created. Since it operates though this tunnel is it able to bypass the firewall since no ports need to be opened. This makes the reverse SSH Shell a good tool to maintain a backdoor in a network being attacked.  

![Flask Project Layout](https://github.coventry.ac.uk/mahone10/Report/blob/master/Github/layout.png)

In addition to programming the reverse SSH server and client I also worked on several other tasks. These include, hosting the C2 server and maintaining it, creating the MySQL database and maintaining the MYSQL server and programming the basics for the webapp since I had prior experience with Flask.

Using my experience with building webapps and Flask I created the basics for the rest of my team to work with. Instead of using a single module approach to the app I opted for a package approach to the project. This means I needed to define classes for models and forms in models.py and forms.py. In addition to this I created the code for Registering users, Login, manging users and access to certain pages. Once I had created the base, I passed the code over to Morgan Smith who worked on the front end since he had more knowledge in this area.

For the c2 server I rented a VPS and loaded it with Ubuntu Server. I installed MySQL server and granted access to a remote account so the database could be modified during production. On top of this, I ran a production version of the webapp on the server which can be accessed over the internet. The c2 server also hosted the SSH server script which waits for connections from the client script and opens a reverse shell.

# Reverse SSH Server &amp; Client

The SSH server does a lot more than just create a reverse shell with the client. Since data from the connection needs to be stored in the MySQL database it needs to be able to create new entities and update current ones. The chart below shows the basic way the connection works with the MySQL database. The MySQL database is an effective way to keep track of Users registered on the webapp and clients devices (Beagle Bone Backoors). When a user is registered their password is encrypted using sha256 and stored in the database. In the login page the password supplied is hashed and compared with the hash in the database.


![FlowChart of SSH Server](https://github.coventry.ac.uk/mahone10/Report/blob/master/Github/Flowchart.png)






A problem I had with paramiko was the inability to change directory. I had to code a work around for this into the SSH client script.

```python
try:
    command = client_session.recv(1024).decode(&#39;utf-8&#39;)
    if command[:2] == &#39;cd&#39;:
        x = command[3:]
        os.chdir(x)
    else:
```

If a command is sent from the server it decodes it to a human readable format and checks if the first two characters are equal to the string &quot;cd&quot;. If this is true, then we set the variable x to the rest of the command which would be the target directory. Finally, we execute os modules built-in command to change directory and pass x into the function.

In our project we are using the backdoor.py script (found on github) which includes the ssh\_client.py code as well as some added code which gives us some stats which can be useful for debugging and providing us with some information. However, if we were using this tool for an active attack on a network, we would use the similar ssh\_client.py script since only contains the necessary code to run on the beagle bone backdoor. Both scripts will be actively trying to connect to the server until a connection is established this means even if the connection is lost between the client and the c2 server once the client reconnects to the internet it will connect to the c2 server.

I also created a version of this system that could be used as the main attack vector. I complied the ssh\_client.py script to an exe which runs the code in the background and isn&#39;t detected by AV software. Using social engineering or a phishing attack then we could trick a user to download and run the program and thus giving us a reverse shell on the victims PC. However, if we did use the reverse shell for this purpose, I would recommend hiding the c2 servers IP and implementing ways to communicate changes to the IP if the server is changed. One way of doing this could be to use web blogs and forums to control operations and hide the IP of the c2 server which the client would find and use.

For our project the reverse shell is just used to gain a backdoor into the network which is used as a staging area for other attacks. Harry Groocock was tasked with programming the actual tool which will be placed on exploited systems. His tool is operated using a txt file and has the ability to take pictures, use the webcam and has a built-in keylogger. We planned on finding and gaining access to systems that have the eternal blue exploit.
