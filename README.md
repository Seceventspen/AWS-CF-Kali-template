# AWS-CF-Kali-template
AWS Cloud Formation Template to deploy a Kali Linux VM and related packages

---

# AWS Kali EC2 Build and Cloud Formation Template

## (1) AWS Kali Cloud Formation Template

You can grab the AWS Kali Cloud Formation Template from within this repo:

- `master-kali-ec2-instance.yaml`

Once you have the file, rename from 'master-kali-ec2-instance.yaml' to your name, for example 'yourname-kali-ec2-instance.yaml'.

Now we'll do a check on the version of the Kali AMI in the template, to ensure it matches the current version of the AWS Marketplace AMI for Kali Linux. This can be done by:

```
aws ec2 describe-images --filters "Name=name,Values=kali-last*" "Name=is-public,Values=true" --query 'sort_by(Images, &CreationDate | reverse(@))[].{Name: Name, ImageId: ImageId, ReleaseDate: CreationDate, tag: Description}''
```

_note: you will need to have the AWS cli installed and configured prior to doing the above._

You should see something that looks like this:

```
[
    {
        "Name": "kali-last-snapshot-amd64-2023.3.0-804fcc46-63fc-4eb6-85a1-50e66d6c7215",
        "ImageId": "ami-0eb546bea6ed49174",
        "ReleaseDate": "2023-08-23T15:19:04.000Z",
        "tag": "Kali Linux kali-last-snapshot (2023.3.0)"
    },
    {
        "Name": "kali-last-snapshot-amd64-2023.2.0-804fcc46-63fc-4eb6-85a1-50e66d6c7215",
        "ImageId": "ami-09082a509a1e43235",
        "ReleaseDate": "2023-05-30T21:42:44.000Z",
        "tag": "Kali Linux kali-last-snapshot (2023.2.0)"
    }
]
```

Once you have done the above, complete the next steps prior to heading over to the Cloud Formation page.

Update the following line:

- Lines 11 & 15: CidrIp: xx.xx.xx.xx/32  # Replace with your desired IP addresses
- Line 27: KeyName: your-key-here  # Replace with your SSH key pair name - should already be in the AWS key vault - of not create a keypair first.
- Line 49: Value: xxxxxx-Custom-Kali # Your custom name here, replace the 'xxxx'
- Line 53: Value: "@xxxxx" # Enter your name here, replace the 'xxxx'

## (2) AWS Cloud Formation Page

Now that you have the template, verified the Kali AMI ID and updated the lines in the config mentioned above, you can now head over to the AWS Cloud Formation page:

https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/

_Note: you'll need to already be logged in_

Then its as simple as:

- Create Stack with new resources (standard)
- 'Template is ready' radio button should already be checked
- Check the ' Upload a template file'
- select the 'Choose file' option
- Select the 'yourname-kali-ec2-instance.yaml' file
- Select 'Next'
- Create a 'Stack' name (pick something relevant/specific to you)
- Select 'Next', 'Next' and finally 'Submit'

## (3) AWS EC2 Dashboard

Now head back to the 'EC2 Dashboard' and look for you EC2 instance name. Then sit back and wait about 15mins or so for everything to initiate, build and finalise.

Upon completion, you should now have:

- a t2.mcrio EC2 Kali Instance within your VPC in us-east-1
- 30gb of storage
- 2 x custom SSH inbound rules
- Kali Linux headless toolset (see the link below for more details)
- SecLists repo installed

Cheers Easy!!!

## [!] Useful/Trouble Shooting Tips

Check Instance Logs: After the EC2 instance has launched, you can check the instance logs to see if there are any errors or issues during script execution. You can access the instance logs using AWS Systems Manager (SSM) or by connecting to the instance and examining the following files:

- `/var/log/cloud-init.log`
- `/var/log/cloud-init-output.log file`

Cloud-Init Status: CloudFormation uses Cloud-Init to execute user data scripts. You can check the status of Cloud-Init by examining the /var/lib/cloud/instance/boot-finished file. It should contain the word BOOTFINISHED when Cloud-Init has completed its tasks.

If you want to alter the Kali packages being installed via the bash script in the template, consult here for additional options:

Kali Meta Packages
- `https://www.kali.org/docs/general-use/metapackages/`

Current install is 'Kali-linux-headless'
- `https://www.kali.org/tools/kali-meta/#kali-linux-headless`
  - note: this should be more than enough for anyone's needs when using kali without a desktop/GUI environment
