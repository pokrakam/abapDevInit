# abapDevSysInit
ABAP Development System Configurator. 
Work in progress, expect bugs and changes, may break stuff, use at own risk.

# Purpose
To automate the repetitive tasks in setting up and personalizing a new ABAP Developer Edition or Trial system. Several actions can be selected, such as setting up the user profile and abapGit environment, and pulling a list of repos from GitHub.

See projects page to see what's planned

# Usage
## Installing
The design is to have everything in one report for ease of installing by copy-paste
as abapGit may not be available. Configuration is also done by copy-paste and editing 
an ABAP include.

If abapGit is already installed, pull as usual. If not, create package `$ZDEVSYSINIT` and report `zdevsysinit` and copy-paste 
the source from [zdevsysinit.prog.abap](https://raw.githubusercontent.com/pokrakam/abapDevSysInit/main/src/zdevsysinit.prog.abap).

## Configuration
In order to keep the configuration handy for repeated installs, it is done by setting variables in an include that can be stored locally. The only two parameters available for input are the GitHub user and password that may be sensitive info - particularly the password should not be stored as free text.

Create include `zdevsysinit_params` in package `$tmp`, and copy the initialization parameter examples from [zdevsysinit_sample.abap](https://raw.githubusercontent.com/pokrakam/abapDevSysInit/main/zdevsysinit_params.sample.abap) 
and update with appropriate values.

Do not put `zdevsysinit_params` in the same package as this may include personal 
information that should not be published on GitHub. Instead, save a copy on your 
laptop on in a Gist to reuse the next time you set up a new dev/trial system. 
It is highly recommended not to put tokens into the source code, or to delete 
them once the RFC destination has been created.

## Running
If the program has been copy-pasted into the system, the selection screen texts will be updated the first time it is run. Please restart the program for this to take effect.

The actions that can be carried out are listed as checkbox items: 

|Action|Description|
|------|-----------|
|Update User Profile|Sets the user data and settings specified in `zdevsysinit_params`|
|Set up GitHub RFC Destination|Creates an RFC destination GITHUB in order to securely store a user and API Token in the SAP database. These can be specified in `zdevsysinit_params` or directly in the selection screen using the additional input fields GitHub User and GitHub Token, as at least the token should not be permanently saved in a plaintext file|
|Install SSL Certificates|Sets up the SSL Certificates needed for communicating to GitHub|
|Get abapGit Standalone|Fetches the latest version of ZABAPGIT_STANDALONE into package $TMP|
|Create abapGit Standalone Exit|Creates user exit to enable abapGit Standalone to access GitHub private repositories using the User/Token supplied above|
|Pull Repos (Overwrite local!)|Pulls all the repositories specified in `zdevsysinit_params`|

On startup, `zdevsysinit` checks what is in the system and proposes actions accordingly. For example if `ZABAPGIT_STANDALONE` is present then "abapGit Standalone" will be unticked. 
Tick any of the boxes to perform the action anyway, e.g. to fetch the latest version of abapGit Standalone and overwrite the local copy. (Note that the one in A4H 2022 is outdated and should be updated on a new system, so please check this box at least one)

*** Please Note *** The action "Pull Repos" will always be unchecked as this is a danger zone. It will overwrite all local repos specified in `zdevsysinit_params` and any local updates will be overwritten. If you wish to use this to regularly pull some public repo updates then comment out or delete any private repos that you may be working on after the first pull into a new system. However it may be a better alternative to set those as background pull and run abapGit as a background job.

Some abapGit repos may fail to import for various reasons that are outside the scope of this app. For example package conflicts or other situations where abapGit needs user input. The message generated by abapGit should be shown in the log. The quickest solution is to run abapGit and import them manually. In most cases the repo should already have been created and can simply be selected from abapGit and pulled.
