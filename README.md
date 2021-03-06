# Automating with Jenkins
![](Diagram2.png)



### Create Jenkins pipeline

Item 1
- New Item >> give it a name >> Freestyle Project
- Add description (optional)
- Discard old builds >> Max # of builds to keep	>> 3
- Build >> add build step >> Execute Shell
- add command `uname -a`
- Post-build Actions >> Build other projects >> Item 2 >> Trigger only if build is stable

Item 2
- New Item >> give it a name >> Freestyle Project
- Add description (optional)
- Discard old builds >> Max # of builds to keep	>> 3
- Build >> add build step >> Execute Shell
- add command `echo "The last build has been successful"` `ls -a`

Actions
- Item 1 >> `Build Now`
- Go to Item 2
- In Build History >> latest `Console Output`
- Finished: SUCCESS


### Generating new private/public keys and connect to Jenkins and EC2
```python
ssh-keygen -t ed25519 -C "your_email@example.com"
```
- Copy public key from .ssh folder >> `cat keyname.pub`
- In the repo >> Settings >> Deploy keys >> Add key
- Paste public key
- New Freestyle Project
- GitHub project >> `https://github.com/rurbonas/eng89_multi_server_automation.git`
- Office 365 Connector >> Restrict where this project can be run >> choose `test-ubuntu-node`
- Source Code Management >> Git >> `git@github.com:rurbonas/eng89_multi_server_automation.git`
- Add new Credentials >> Jenkins
- Kind >> SSH Username with private key
- Private Key >> Enter directly >> Add
- Copy private key from .ssh folder >> `cat keyname`
- paste in Jenkins key
- Branches to build >> Branch Specifier (blank for 'any') >> `*/main`
- Build Triggers >> GitHub hook trigger for GITScm polling
- Build Environment >> Provide Node & npm bin/ folder to PATH
- Build >> add build step >> Execute Shell
- `cd app
npm install
npm test`
- Build and check if running

# Jenkins Pipeline with GitHub
### Create a web-hook for Jenkins

- Copy Jenkins IP address from URL
- Go to GitHub >> Repo Settings >> webhooks >> add webhook >> Payload URL `http://[JENKINSIP:PORT]/github-webhook/`

### Git Publisher
- Add Post-build Actions to previous item and connect to:

- New Freestyle Project >> `name_merge`
- Discard old builds >> Max # of builds to keep	>> 3
- GitHub project >> `https://github.com/rurbonas/eng89_multi_server_automation.git`
- Office 365 Connector >> Restrict where this project can be run >> choose `test-ubuntu-node`
- Source Code Management >> Git >> `git@github.com:rurbonas/eng89_multi_server_automation.git`
- Add Credentials
- Branches to build >> `*/dev`
- Additional Behaviours
```
Name of repository >> origin
Branch to merge to >> main
Merge strategy >> default
Fast-forward mode >> --ff
```
- Build >> add build step >> Execute Shell
```
git checkout main
git merge origin/dev
```
- Post-build Actions >> Push Only If Build Succeeds >> Branches >> 
```
Branch to push >> main
Target remote name >> origin
```