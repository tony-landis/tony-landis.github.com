--- 
layout: posts
categories: uncategorized
title: Asterisk PBX Voicemail - Configuring and Sending Emails the Easy Way
tags: asterisk pbx email
---

Asterisk PBX Voicemail - Configuring and Sending Emails the Easy Way
====================================================================

I have been very disappointed with the voicemail emailing capabilities of Asterisk PBX. If you need to use a SMTP host, it can be a time consuming task to configure sendmail, postfix, etc, to use an external SMTP provider. And once you get the emails to send, customizing and scripting is very limited.

I wanted to be able to use SMTP, add BCC and CC recipients, have full scripting control over the message body and subject, and attach the voicemail recording in the e-mail. It seemed like writing a script and using the 'mailcmd' setting in the [general] section of the asterisk voicemail.conf file was a suitable way to handle this, so I took this route.

First, my asterisk voicemail.conf entries (the mailcmd, emailsubject, and format settings must be adhered to):
<pre><code>[general]
maxsilence=6
maxmessage=360
minmessage=4
delete=no
mailcmd=/var/www/voicemail-send.php
emailsubject=${VM_MAILBOX}|${VM_MSGNUM}|${VM_CALLERID}|${VM_DUR}
format=wav

[default]
100 =&gt; 101010,Tony,my@email.com</code></pre>

Then I dropped the PHP script below into my /var/www/ directory with a name of voicemail-send.php and 
ran chown asterisk:asterisk on it to be sure asterisk can execute. 

You will need to download the 
<a title="PHPMailer" href="http://sourceforge.net/project/showfiles.php?group_id=26031" target="_blank">phpmailer library</a> 
and set the $mail_lib_path variable with the path to that library, 
as well as make sure the $ast_vm_path is correct. 
The email/smtp variables will also need to be set with your correct information.

<script src="http://gist.github.com/69820.js"></script>

That is all... Asterisk will now call voicemail-send.php after each voicemail is saved and if everything is configured correctly as described above, the voicemail email alert and attachment will be sent to the email address for the mailbox configured in the asterisk voicemail.conf file.

You can run the voicemail-send.php script from the command line and enter these lines (then press return about 4 times) to test without actually placing calls, where your@email.com is a real email address and 195 is a real mailbox and 1 a real msg000NUM.wav file to be attached:

<pre><code>To: "Test User" &lt;your@email.com&gt;
Subject: 100|1|8881234567|12:22</code></pre>
