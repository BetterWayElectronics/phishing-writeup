# Bespoke Phishing Against vSphere & Slack (QUT Students)
### Attack Goal
The goal was to obtain the credentials for vSphere, this seemed like the most highly prized goal as compromising the accounts would potentially allow root access on all of the users VM’s. I also sought for a methodology that has no real technical input or output and can be achieved quickly and easily with a high success rate. Given the ambiguity of whether I was allowed to go for this target I obtained permission from Matthew McKague (Unit Coordinator) prior to attacking all of the students. 
### Required Tools & Justification
Achieving this can be done in many ways but I personally used the resources available to me, which was my website and prior experience in designing and implementing phishers manually. From here I could use PHP to send the spoofed emails as well as obviously hosting the phishing page on the same website. Should this have been a real world scenario I would have hosted my own VPS as so I could have proper access to PHP settings which would allow further spoofing of the location of the emails, which are read within the header of each sent email. I sought not to use the SE Toolkit as this was a novice methodology which was limited and restrictive.
### Methodology/Implementation
The target website to spoof was ‘https://merlin.scitech.qut.edu.au/’ as this is what we were told to visit in order to access vSphere, but this page simply is a place holder from here all users will click on a link to the actual login page located at ‘/vsphere-client/’. However when I first tried to copy the website, viewing the source of that page did not actually display any login forms. It appeared that the page simply hosted an iframe of the actual login, hosted on a different website.
 
![](https://i.imgur.com/oOS43kJ.png)
 
It appeared then that the login page was actually ‘https://fstugrad01vc.qut.edu.au/vsphere-client/logon’. This page indeed had the login form that I needed.
 
![](https://i.imgur.com/P4p0fjF.png)
 
In order to install a phisher on this login form I had to modify it, given it wasn’t an actual form I was used to I gave it an actual form function which encompassed the entire div id ‘loginForm’. From here I added an action to the form, being ‘login.php’ with the method ‘post’. I also added input names to username and password, of which will become part of the post method. I also changed the login button to submit the form by adding the action ‘submit’. This did not visually change anything for the end user.
 
![](https://i.imgur.com/c6jD4zg.png)
 
Now at this point if I were to put it into my own ‘index.html’ file and run it on my computer it would display nothing but the form. This is because the copied source references sub-directories that I simply do not have. Now, given that this page is only accessible from the university network I felt that it would be pointless to grab these referenced files and host them on my website – should a user forget to log into the university VPN and access my phisher, it would work as though they were on it. This is incongruent and unrealistic. So to fix this I simply added the actual complete URL to the referenced files.
 
![](https://i.imgur.com/HfE4Owi.png)
 
This source list, referencing its own website now turned into this:
 
![](https://i.imgur.com/3sQoM60.png)
 
Now, when run on my own website and visited through the university VPN, the phishing page works and appears as though it was the real thing (submission animations etc), albeit if you ignored the URL.
 
![](https://i.imgur.com/DSM3qry.png)
 
Now I had to assemble a login.php to grab the credentials that were entered. This can be as simple as making a variable read the ‘POST’ command of the phisher and outputting it to a text file, but surprisingly doing this caused my antivirus to consider it a threat. 
 
![](https://i.imgur.com/Bg0Ke2d.png)
 
So instead of a simplistic and overused method I decided to make it my own and in-turn more complex and more useable with greater information about each victim.
 
![](https://i.imgur.com/Ckxo0jg.png)
 
In this example the victim’s browser information along with their username and password is sent via email to me and also stored in a log file. By having the previous form ‘POST’ information about the login form, the above PHP is able to retrieve that information and log it. By adding a ‘header’ command once this logging process is complete the user is then directed to the real vSphere site after ‘logging in’. Should they have already the requisite cookies in their browser they will have literally logged in, else they will be presented with another login page. Now obviously my website is not vSphere and my url is not vSphere, but I am on an Australian website with SSL/TLS and given that there is a strong chance the actual address bar is overlooked I was confident that this was enough. I was not able to find an XSS on any university websites that would aid in masking the actual address bar, either through an iframe or another method.

But how would I distribute this phishing page? Well I would simply email each victim a link to this website. This way I could falsely send it from an authority and be able to mask the URL briefly though an HTML based email. I again chose to host an email spoofer on my website and build it myself. So then upon reading various emails I had received from the staff for this subject I was able to craft a realistic email from a realistic source. I falsely proclaimed to the victim that there was an issue with vSphere and to use its new mirror, of which has an additional and much requested feature. This was enough to even sprout a discussion from a friend whom I emailed.
 
![](https://i.imgur.com/YDwL8L9.png)

Sending the phishing emails was the easiest to thing to create, given that using PHP is a very legitimate way of sending emails and I had already made something like this for my own websites in the past. I password protected the actual function of the phisher just in case of somebody discovers it; I also added a .htaccess file to block access to the email spoofing page and the log output. 

![](https://i.imgur.com/ZYzscAK.png)

The actual email sending page control panel appeared as such (I had a lot of fun with this part):

![](https://i.imgur.com/ryEFusI.png)
 
By having a form, rather than a script do all the work I was able to customise each email and recipient. Going with the theme of customisation I also ended up customising this page as well as the log output. I also had the later realisation that sending an email one at a time was not ideal, I ended up customising my PHP script to allow an array of emails to be blind carbon copied.
These are what some of the emails ended up looking like:

![](https://i.imgur.com/SJJ46W5.png)

While the above URL may look out of place, it was not uncommon for them to be this long.

![](https://i.imgur.com/ssPygID.png)
 
I initially grabbed student emails from the subject’s Slack page. Roughly 90% of the students who signed up to slack used their student ID email, of which was visible in their profile page. This allowed me to create a mailing list for the phisher, allowing me to send an email to a large proportion of the class. I later then used every email and made updates as newer students signed up to the Slack page.

### Outcome

![](https://i.imgur.com/OB3EuDq.png)
 
The outcome was that 70% of the targeted emails yielded a result. This meant that I had potential access to their vSphere accounts and even their student accounts at the university, and ultimately anything that can be pivoted from there. Given I had access to vSphere, the original goal, I ultimately had root on all of their machines. This is because regardless of their logged in state I could simply restart the machines into the GRUB menu and modify it to allow for a bash shell. This would allow me to reset their root password. 
![](https://i.imgur.com/XgiltBn.png)

I then repeated this attack with a phisher for the Slack website, again with permission. This yielded an additional set of credentials, most of which used different passwords to vSphere which was a positive for them. Unfortunately I saw that a lot of students did not sign up with their student ID which would allow for an opportunistic pivot – alas this was not my goal or within the scope of my permission. To get the Slack phisher to work I had to modify my mailing PHP to send base64 emails, this would allow me to properly clone the html based email that Slack sends out to people.

### Reflection
While this was successful, I discovered an issue with my fake emails. I could only send emails to ‘STUDENTID@qut.edu.au’, with all emails sent to ‘STUDENT.NAME@connect.qut.edu.au’ being filtered as they failed their authentication, reason 601. Microsoft’s Office365 website states that the error means it failed “implicit email authentication, and the sending domain is one of your organization's accepted domains (this is part of self-to-self, or intra-org, spoofing)”.
 
![](https://i.imgur.com/dJkkpNI.png)

Emails sent to @qut.edu.au will succeed but will have a different error, but they will still enter the inbox of the victim just fine. Should the recipient read this header information, it is clear that the email is spoofed. There is a lot more information in the header that illustrates this and is difficult to mask without the ability to modify the webserver’s PHP settings.

![](https://i.imgur.com/ZeAxa0O.png)

Eventually though the messages to @connect.edu.au will go through to the inbox, but quarantined.

![](https://i.imgur.com/nYXTRQW.png)
 
I also noticed that the Slack phishers, encoded in base64 indeed reached @connect.edu.au emails without being filtered and or quarantined. It seems that encoding the email may have bypassed the previous issues I had. I am not sure why, but it was inconsistent and I did not have the time to test.
From a forensic perspective this attack is hopeless. There are too many elements that expose who I am and where the phisher is and what it is doing. But this attack can easily be prevented by using 2FA on vSphere and Slack and teaching students about phishing - though with a bit of effort the 2FA page could also be spoofed. Further methodologies to mitigate this would be to disallow emails with the from address of @qut.edu.au and @connect.qut.edu.au without associating it with the actual QUT mail server. Though stopping phishers from other sources is more challenging, perhaps associating it with known bad ASN’s could help, though would not stop my phisher.
