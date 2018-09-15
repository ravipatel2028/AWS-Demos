# Execute user data after the initial launch of my EC2 instance

By default user data is executed once, at the first boot of the instance. There are situations that would benefit from the ability to execute user data a second time. May be dump some data to logs or recover SSH keys

Follow this article in **[Youtube](https://www.youtube.com/channel/UC_evcfxhjjui5hChhLE08tQ/playlists)**

![Execute user data every boot](https://raw.githubusercontent.com/miztiik/AWS-Demos/master/How-To/setup-ssh-key-recovery-using-userdata/images/setup-ssh-key-recovery-using-userdata-valaxy-00.png)

### Prerequisites
1. EC2 Instance [Get help here](https://www.youtube.com/watch?v=KDtS6BzJo3A) - _Without Keys_
1. Recovery Server - 

### Generate the Authorized Keys from `Recovery Server`
Login to the `Recovery Server` and execute the following command and copy the output as shown. _Leave out the red underlined text_
```sh
more /home/ec2-user/.ssh/authorized_keys
```
![Execute user data every boot](https://raw.githubusercontent.com/miztiik/AWS-Demos/master/How-To/setup-ssh-key-recovery-using-userdata/images/setup-ssh-key-recovery-using-userdata-valaxy-01.png)

### Update the key in the old Server(the one without keys)
1. Stop the server.
1. Update the user data field with the code updated with the key.
   - In the below commands update your recovery key, after `echo -e`  _between quotes_
   ```sh
   Content-Type: multipart/mixed; boundary="//"
   MIME-Version: 1.0
   
   --//
   Content-Type: text/cloud-config; charset="us-ascii"
   MIME-Version: 1.0
   Content-Transfer-Encoding: 7bit
   Content-Disposition: attachment; filename="cloud-config.txt"
   
   #cloud-config
   cloud_final_modules:
   - [scripts-user, always]
   
   --//
   Content-Type: text/x-shellscript; charset="us-ascii"
   MIME-Version: 1.0
   Content-Transfer-Encoding: 7bit
   Content-Disposition: attachment; filename="userdata.txt"
   #!/bin/bash
   /bin/echo -e "ssh-rsa    AAUvoqDuvCKFrVzeq/O68JgAo0zSSD3KMYwO1RSZ8/2FwMEYZP7jAh3GOYJhIS
   AzFsDcN/jgtluZIwEn7MXym21EDLk1aFdI20WtbQJH79as9   +nV9jtzf9BiQnM/fe18Frb94A1DUALcEyPesloYvcOxyCCaqAKS6v1g1me4Up+IbHNfVgE   +GtLdh+oohR8SRc3xL9
   tvQu0kzFSRVsfymhu5l2WBpf9STvm3rtMbNKzjmKAqPlMSuShn72pTwqScGoPG+3ofZ36n"    >> /home/ec2-user/.ssh/authorized_keys
   --//
   ```
1. Start the server
1. Login with the `Recovery Server` Key

##### References
[1] - [AWS Docs](https://aws.amazon.com/premiumsupport/knowledge-center/execute-user-data-ec2/)