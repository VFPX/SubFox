# SubFox User's Guide
**Seamless integration for Subversion source code control**

This documentation is intended for FoxPro developers who wish to **use** SubFox as it was intended -- not for those interested in _modifying_ or _participating_ in the SubFox project (that is the [SubFox Design Documentation](SubFoxDocs.md)).

## Installation
To start using SubFox (i.e as a user -- no source code) here are the steps:

* Download and install [SVNCOM from PushOk](http://www.pushok.com/soft_download.php?idprogram=7). You will need to choose the latest "SDK installation" (either the 32 bit or 64 bit version). The 64 bit version puts the files into the "Program Files(x86) folder regardless of where you ask to put it, so then you must manually copy the "PushOk" folder into the "Program Files" folder as well (bug to be fixed).

* Download and install [TortoiseSVN](http://tortoisesvn.net/downloads). SubFox is designed to fulfill day-to-day requirements. You will need Tortoise to cover the remaining occasional tasks.

* Download the [SubFox.exe](http://vfpx.codeplex.com/Release/ProjectReleases.aspx?ReleaseId=39185#DownloadId=102344) into any folder you like. I prefer the VFP home path but the Windows or System folders work fine, or you can create a folder in the Program Files area.

* If you have a licensed copy of [Stonefield Database Toolkit](http://www.stonefieldsoftware.com/stonefield-database-toolkit-sdt/) and would like to make it available to SubFox (required for database versioning) create a Windows Registry key for {"HKEY_LOCAL_MACHINE\Software\SubFox"}, and within it create a string value named SDT. Set that string value to the full path where the SDT.APP file is installed.

## Setting up a SubFox Project
* Find out the URL of your Subversion server. This might mean you need to install [VisualSVN](http://www.visualsvn.com/visualsvn/download/) for you to test with, or you might want to join an existing project and you simply need your administrator to tell you the web-URL, user-ID and password to work with.

* Find out the sub-URL of your project on that server. If you decide to create your own for testing, please take the advice to build the standard sub-folders _branches, tags and trunk_ which will payoff down the road. Typically the final combined URL looks like **https://MyDomain.com:8443/svn/MyProject/trunk**.

* Create a folder for your project and link it to the Subversion server using the Tortoise command **SVN Checkout** (in Windows Explorer, right click on your folder). **_Beware_** that Subversion does something slightly unpredictable when you download into a folder with files in it that match files coming down from the repository -- it leaves them _unchanged_ but flags them as _modified_. If you want Subversion to replace these files with the version in the repository (what you would expect when downloading a complete project) you will have to wait until the download is complete, then execute a **revert** on those files!

* Start Visual FoxPro and load the SubFox menu of features with the command **DO SubFox.exe**. You may need to include the path to the program such as **DO (home()+"SubFox.exe")**. To automate this step, go to the Tools->Options->File Locations->Startup Program and register the SubFox.exe to run here.

* Open the VFP project file:
    * For projects yet to be uploaded to Subversion -- create your VFP project as you normally would (or already have). Walk through the list of files and make sure that all files are located within the sub-tree of the project's home directory, and that the project file itself is directly in the project's home directory.

    * For projects just downloaded from Subversion -- close all open projects, then go to File->SubFox->Translate Source Files. Answer YES when prompted to open a project. Set _Files of type_ to _SubFox Project_ (the default setting). Navigate to your project folder and select the project file that was downloaded from the Subversion repository (it should have the format _MyProject.pjx.subfox_). SubFox will create a new VFP project and load into it all of the files downloaded by Subversion. You should be able to click _cancel_ on the translator window that pops up since SubFox will automatically decode all of the source files during the project bootstrap. However, the window will appear just the same.

* Go to _File->SubFox->Setup Versioned Files_. If your project contains external files -- files marked as excluded -- that you want to include in the Subversion repository, the setup editor will let you choose which of these _excluded_ files to include in the Subversion project. It also allows for identification of which database containers (DBCs) are to be tracked, and which tables -- both free tables and contained tables -- are to be treated like source code.

* If this project is being moved to Subversion for the first time, go to _File->SubFox->Upload to Repository_. Here you will have the chance to pick-and-choose which files to include in the upload and assign a message to label the reason for the upload -- preferably something like "Initial Upload".

## Day to Day Usage
SubFox is designed to automate the regular tasks of using Subversion, but what are those tasks and how is it supposed to work? The normal cycle looks something like this:

* Download the latest version before starting work.
* If there are any conflicts (normally there won't be any):
    * Resolve them immediately and 
    * Upload _those changes_ right away.
* Work upon the project -- design, code, test, debug, etc.
* Occasionally use the Download function to pickup new versions that may have emerged while you were working. If any conflicts occur, try to resolve them sooner rather than later.
* When you have a block of changes that are ready to capture:
    * Download the _very latest_ changes already submitted by other developers (you cannot upload unless your local working-copy is fully up to date).
    * Resolve any conflicts that may have resulted (you cannot upload files that are conflicted).
    * Upload your changes as a block.
* At the end of the day, it is generally a good practice to upload your changes as a precaution against hardware failure, theft, fire, etc. It is not, however, recommended that you upload changes that might potentially interfere with other developers by breaking otherwise working parts of the application.

## Resolving Conflicts
Sometimes when you download from the repository you will encounter a **conflict**. This is because both you and another user have edited the same line (or block) of code at the same time, effectively stepping on each others coding. In a well coordinated shop this simply shouldn't happen, but in a world-wide collaboration like an open-source project, this is rather typical. When this happens, you must use a conflict resolution editor (included with Tortoise) to merge together your changes and the changes made by the other developer(s) that you're in conflict with.

If this seems like a tug-of-war, it's really not. The other developer beat you to the punch, and saved his/her changes without any trouble. In fact, there might have been several people that came and went, changing it over and over again. So far as the rest of the world is concerned, it is only _you_ that is a trouble-maker, since you are trying to submit changes to a dinosaur block of code that is no longer part of the project. That is what makes it your problem to figure out how these separate sets of changes can be incorporated into the final program.

Conflicts get resolved in basically three ways:

* You can simply **revert** your version, effectively discarding all of your changes and accepting those produced by other developers.

* You can go with the all-or-nothing approach, choosing to **discard all** of your own changes or **discard all** of everyone elses changes. If this sounds callus, it's not always so. This is precisely what you should do with changes to bitmaps and icons. Even in the case of source code, typically you compare the versions side-by-side and choose _take mine_ or _take theirs_ for a block of code because usually the change will be the same, or nearly the same. This happens a lot when separate developers both discover the same bug and each fix it sightly differently -- perhaps by putting a comment next to the change.

* You can rewrite the whole block of code so that the essence of all changes are incorporated into the final result. This is the most time-consuming method, and the one that is most likely to lead to new bugs, more testing, etc. However, when completely unrelated changes take the same section of code in two different directions, there really is no other choice. Often times this type of change is done collaboratively with the other developer(s) involved.