# Mediawiki Utility Collection

This repository is a collection of small utilities used for automating things around mediawiki

Currently it consists of two python scripts:
- upload.py
- uploadFile.py

## upload.py
This python script is used for creating pages or uploading content to pages based on MediaWiki Markup files in a directory tree. The current implementation assumes that (or works best with) subpages being enabled. 
The script required various command-line arguments which we will explain below. There are two common case arguments "-h" and "-v" that print the parameters and enable verbose output respectively. 
... more interesting are the other parameters:

| Short | Long | Description |
| ------ | ------ | ------ |
| -u | url= | The url of the wikis action endpoint! Default is <domain>/w/api.php or just <domain>/api.php |
| -b | basedir= | The base directory where the files can be found. Eg. /home/test/wikipages |
| -e | ext= | The file extension of the files to search and upload. E.g. .txt or .mw |
| -a | application= | This is the parent page name |
| -c | category= | If a category is provided the page name will be prefixed with "<category>:" so that the page (and subpages) are created within that category |
| -l | login= | The wiki login name. Please not that you must create a bot user for using the wiki action api. This is the name of the bot |
| -p | password= | This is the bot's password created by the wiki |

Maybe some more words on the logic behind the directory tree, the files (with the extension "ext") and the created pages.
Let us assume we following situation:
```
basedir = /home/test/wikipages
ext = .mw
application = CoolService
category = Microservice
login = homer@bot
password = oisjdf092u3r9u2nvsdß0 
```

furthermore assume the following directory/file structure (below basedir):
```
./installation/systemrequirements.mw
./installation/deployment.mw
./installation.mw
./main.mw
```

There are 4 pages generated (this one you didn't expect, right ;-)) ... but well, let's look at the final titels (fully qualified pagenames): 
1. Microservice:CoolService
2. Microservice:CoolService/installation
3. Microservice:CoolService/installation/systemrequirements
4. Microservice:CoolService/installation/deployment

Please note the first one and furthermore notice the "main.mw". This is kind of special: If there is a file with the name "main" there is not a page called "main" generated but it is treated to contain the main page (content) of the current page level, i.e. the content of "Microservice:CoolService". 
The meaning of the "current page level" is that
```
./installation/main.mw
```
and 
```
./installation.mw
```

would produce the same wikipage (assuming the content of main.mw and installation.mw is equal). 

## uploadFiles.py
This python script is used for uploading file (images respectively). 
The script required various command-line arguments which we will explain below. There are two common case arguments "-h" and "-v" that print the parameters and enable verbose output respectively. 
... more interesting are the other parameters:

| Short | Long | Description |
| ------ | ------ | ------ |
| -u | url= | The url of the wikis action endpoint! Default is <domain>/w/api.php or just <domain>/api.php |
| -b | basedir= | The base directory where the files can be found. Eg. /home/test/wikiimages |
| -e | ext= | The file extension of the files to search and upload. E.g. .jpg or .png |
| -l | login= | The wiki login name. Please not that you must create a bot user for using the wiki action api. This is the name of the bot |
| -p | password= | This is the bot's password created by the wiki |

The idea and the mechanics of the parameters are the same as above (upload.py), so we omit the more detailed explainations here.
There is current one major limitation (and kind of work in progress) especially iin combination with the page upload. 
The limitation of mediawiki is that the image name needs to be unique and do not offer namespacing of similar. Thus you might have for different <pagename>s a subpage called "Overview", but if you want to use an overview image on these pages, you need to make the image name unique! This is currently a manual requirement ... we are working on a solution similar to the above "subfolder" naming for images. The challenge is not to rename the image but to find and update all references to that image in pages (in pages to be uploaded with upload.py). 