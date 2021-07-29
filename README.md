# Automating with Jenkins
![](Diagram2.png)

### Generating new private/public keys
```python
ssh-keygen -t ed25519 -C "your_email@example.com"
```

adding keys to github (work in progress)
...

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



