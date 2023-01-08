
# How to compare 2 Salesforce Orgs metadata using DX and git-diff (delta)?

## What are main reasons for the metadata in your version control becomes out-of-date with metadata in the Org
- Obvious one is when users make direct changes to the Org and not committing back these changes into the version control
- If you remove for example, a custom field say (Tree__c.quantity__c) using the org UI, Salesforce correctly removes entries for this field in Profiles (for example), but this change has to be committed into version control. Otherwise your that Profile version control will have a dangling field
    - Tools find out the dangling fields
        - [Dangling Fields](https://github.com/mohan-chinnappan-n/cli-dx/blob/master/mdapi/dangling-fields.md)



## Steps in comparing metadata of two orgs

- Login into two orgs using DX

```
sfdx force:auth:web:login -r https://login.salesforce.com

Successfully authorized mohan.chinnappan.n.sel@gmail.com with org ID 00D4x000007rxoGEAQ

```
----

```
sfdx force:auth:web:login -r https://login.salesforce.com

Successfully authorized mohan.chinnappan.n.sel2@gmail.com with org ID 00D8W000004LymuUAC
```

- Prepare package.xml
    - Add metadata types as needed
``` 
cat package.xml
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Package xmlns="http://soap.sforce.com/2006/04/metadata">
    <types>
        <members>*</members>
        <name>CustomObject</name>
    </types>
    <types>
        <members>*</members>
        <name>Profile</name>
    </types>
    <types>
        <members>*</members>
        <name>Permissionset</name>
    </types>
    <version>56.0</version>
</Package>

```

## Retrieve the metadata from both orgs

- make sure two 2 folders create to store the retrieve results
    - in our case se and se2
```
mkdir se se2
```

### For first Org

```
sfdx force:mdapi:retrieve -k package.xml -u mohan.chinnappan.n.sel@gmail.com  -r se -z
Retrieving v56.0 metadata from mohan.chinnappan.n.sel@gmail.com using the v56.0 SOAP API
Retrieve ID: 09S4x00000E8o9qEAB
Retrieving metadata from mohan.chinnappan.n.sel@gmail.com... done
Wrote retrieve zip to /Users/mchinnappan/org-compare/se/unpackaged.zip
Extracted unpackaged.zip to: /Users/mchinnappan/org-compare/se/unpackaged
```

### For second Org

```

sfdx force:mdapi:retrieve -k package.xml -u mohan.chinnappan.n.sel2@gmail.com  -r se2 -z
Retrieving v56.0 metadata from mohan.chinnappan.n.sel2@gmail.com using the v56.0 SOAP API
Retrieve ID: 09S8W00000DLiy0UAD
Retrieving metadata from mohan.chinnappan.n.sel2@gmail.com... done
Wrote retrieve zip to /Users/mchinnappan/org-compare/se2/unpackaged.zip
Extracted unpackaged.zip to: /Users/mchinnappan/org-compare/se2/unpackaged

```


## Install git-diff in your machine

- For macOs

```
brew install git-diff

```

## Use git-diff (delta) to find the diffs

```
delta --side-by-side -- se/unpackaged/unpackaged/profiles/Admin.profile se2/unpackaged/unpackaged/profiles/Admin.profile 
```
![demo](img/screenRecording.webm.gif)


