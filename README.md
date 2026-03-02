Prerequisites
Git installed


Node-RED installed and running


GitHub account


SSH access configured


Replace ashut0shh with the Git account being used


Create a modify the repo name as per your needs

Install Git (if required)
sudo apt update
sudo apt install git -y

1. Clone Repository
Clone the GitHub repository locally:
git clone https://github.com/ashut0shh/IITK_SMART_PLUG_git_iter1.git
cd IITK_SMART_PLUG_git_iter1

2. Configure Git Identity (Required Once)
Git will not allow commits without identity configuration.
git config --global user.name "Ashutosh"
git config --global user.email "ashut0shh@users.noreply.github.com"
Verify:
git config --global --list

3. Configure SSH Authentication (Password-Free Push)
Generate SSH Key
ssh-keygen -t ed25519 -C "ashut0shh"
Press Enter for defaults.

Add Key to SSH Agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

Add Public Key to GitHub
Display key:
cat ~/.ssh/id_ed25519.pub
Copy output and add it in:
GitHub → Settings → SSH and GPG Keys → New SSH Key

Test SSH Connection
ssh -T git@github.com
Expected result:
Hi ashut0shh! You've successfully authenticated

Switch Repository to SSH
Check remote:
git remote -v
If HTTPS appears, change it:
git remote set-url origin git@github.com:ashut0shh/IITK_SMART_PLUG_git_iter1.git
Verify again:
git remote -v

4. Repository Structure
Each device keeps its own folder:
IITK_SMART_PLUG_git_iter1/
   device1/
   device2/
   device3/
Create device folder (only once):
mkdir device1

5. Locate Node-RED Flow Files
In your setup, Node-RED stores flows here:
~/nodered-data/data/
Files:
flows.json
flows_cred.json
(Do NOT move these files.)

6. Copy Flows Into Repository
Copy real flow files into the repo:
cp ~/nodered-data/data/flows.json ~/IITK_SMART_PLUG_git_iter1/device1/
cp ~/nodered-data/data/flows_cred.json ~/IITK_SMART_PLUG_git_iter1/device1/
Verify:
ls -l device1
You should see normal files (no -> arrows).

7. Verify Flow Tracking
Open Node-RED editor.


Modify a flow.


Click Deploy.


Then run:
cd ~/IITK_SMART_PLUG_git_iter1
git status
You should see:
modified: device1/flows.json

8. Commit and Push Flows
git add .
git commit -m "update flows"
git push
Flows now appear in GitHub with full JSON contents.

9. Git Configuration (Multi-Device Safe)
Prevents conflicts when multiple devices push.
git config --global pull.rebase true
git config --global rebase.autoStash true

10. Automatic Flow Backup Script (Recommended)
Create script:
nano ~/push_flows.sh
Contents:
#!/bin/bash

REPO=~/IITK_SMART_PLUG_git_iter1
SRC=~/nodered-data/data

cp $SRC/flows.json $REPO/device1/
cp $SRC/flows_cred.json $REPO/device1/

cd $REPO || exit

git add .
git commit -m "Auto flow update $(date)" || true
git pull --rebase
git push
Make executable:
chmod +x ~/push_flows.sh
Test manually:
~/push_flows.sh

11. Schedule Automatic Push
Open cron:
crontab -e
Add:



*version control*



1. View history of all flow changes
git log
Example output:
commit 8a21c3...
Author: Ashutosh
Date: ...

update flows
You can see every modification ever made.

2. See exactly what changed between versions
git diff
or between commits:
git diff <commit1> <commit2>
This shows which nodes or JSON sections changed inside flows.

3. Restore an older version of flows
Example:
git checkout <commit-id> -- device1/flows.json
This lets you roll back Node-RED to a previous working configuration.
Very useful if a deployment breaks something.

4. Recover deleted flows
Even if you accidentally delete flows:
git checkout HEAD -- device1/flows.json
Git restores them instantly.

5. Track which device changed what
Because each device commits independently, history shows:
update flows (device1)
update flows (device2)
So you can identify where a change originated.


*/5 * * * * ~/push_flows.sh
Flows will sync every 5 minutes.


