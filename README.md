# A Puppet Control Repository

* [What You Get From This control\-repo](#what-you-get-from-this-control-repo)
* [Copy This Repo Into Your Own Git Server](#copy-this-repo-into-your-own-git-server)
  * [GitLab](#gitlab)
  * [Bitbucket/Stash](#bitbucketstash)
  * [Github](#github)
* [Code Manager Setup](#code-manager-setup)


## What You Get From This control-repo

This is a template [control repository](https://puppet.com/docs/pe/latest/code_management/control_repo.html) that has the minimum amount of scaffolding to make it easy to get started with [r10k](https://puppet.com/docs/pe/latest/code_management/r10k.html) or Puppet Enterprise's [Code Manager](https://puppet.com/docs/pe/latest/code_management/code_mgr.html).

The important files and items in this template are as follows:

* Basic example of roles and profiles.
* An example Puppetfile with various module references.
* An example Hiera configuration file and data directory with pre-created common.yaml and nodes directory.
  * These match the default hierarchy that ships with PE.
* An [environment.conf](https://puppet.com/docs/puppet/5.3/config_file_environment.html) that correctly implements:
  * A site-modules directory for roles, profiles, and any custom modules for your organization.
  * A config\_version script.
* An example [config\_version](https://puppet.com/docs/puppet/5.3/config_file_environment.html#configversion) script that outputs the git commit ID of the code that was used during a Puppet run.

Here's a visual representation of the structure of this repository:

```
control-repo/
├── data/                                 # Hiera data directory.
│   ├── nodes/                            # Node-specific data goes here.
│   └── common.yaml                       # Common data goes here.
├── manifests/
│   └── site.pp                           # The "main" manifest that contains a default node definition.
├── scripts/
│   ├── code_manager_config_version.rb    # A config_version script for Code Manager.
│   ├── config_version.rb                 # A config_version script for r10k.
│   └── config_version.sh                 # A wrapper that chooses the appropriate config_version script.
├── site-modules/                         # This directory contains site-specific modules and is added to $modulepath.
│   ├── profile/                          # The profile module.
│   └── role/                             # The role module.
├── LICENSE
├── Puppetfile                            # A list of external Puppet modules to deploy with an environment.
├── README.md
├── environment.conf                      # Environment-specific settings. Configures the modulepath and config_version.
└── hiera.yaml                            # Hiera's configuration file. The Hiera hierarchy is defined here.
```

## Copy This Repo Into Your Own Git Server

To get started with using the control-repo template in your own environment and git server, we've provided steps for the three most common servers we see: [GitLab](#gitlab), [BitBucket](#bitbucketstash), and [GitHub](#github).

### GitLab

1. Install GitLab.
    * <https://about.gitlab.com/downloads/>
1. After GitLab is installed you may sign in with the `root` user and password `5iveL!fe`.
1. Make a user for yourself.
1. Make an SSH key to link with your user. You’ll want to do this on the machine you intend to edit code from (most likely not your Puppet master, but your local workstation or laptop).
    * <http://doc.gitlab.com/ce/ssh/README.html>
    * <https://help.github.com/articles/generating-ssh-keys/>
1. Create a group called `puppet` (this is case sensitive).
    * <http://doc.gitlab.com/ce/workflow/groups.html>
1. Add your user to the `puppet` group as well.
1. Create a project called `control-repo`, and set the Namespace to be the `puppet` group.
1. Clone this control repository to your laptop/workstation:
    * `git clone <repository url>`
    * `cd control-repo`
1. Remove this repository as the origin remote:
    * `git remote remove origin`
1. Add your internal repository as the origin remote:
    * `git remote add origin <url of your gitlab repository>`
1. Push the production branch of the repository from your machine up to your git server
    * `git push origin production`

### Bitbucket/Stash

1. Install Bitbucket
    * <https://www.atlassian.com/software/bitbucket/download>
1. Make a `Project` called `puppet` (with a short name of `PUP`)
1. Create a repository called `control-repo`
1. Create a user called `r10k` with a password of `puppet`.
    * Make the r10k user an admin of the `PUP` project.
1. Either use the admin user to test pushing code, or create a user for yourself and add your SSH key to that user.
    * If making a user for yourself, give your user account read/write or admin privilege to the `PUP` project.
1. Clone this control repository to your laptop/workstation
    * `git clone <repository url>`
    * `cd control-repo`
1. Remove this repository as the origin remote
    * `git remote remove origin`
1. Add your internal repository as the origin remote
    * `git remote add origin <url of your bitbucket repository>`
1. Push the production branch of the repository from your machine up to your git server
    * `git push origin production`

### GitHub

1. Prepare your local git client to authenticate with GitHub.com or a local GitHub Enterprise instance.
    * <https://help.github.com/articles/generating-ssh-keys/>
    * <https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/>
1. Create a repository called `control-repo` in your user account or organization. Ensure that "Initialize this repository with a README" is not selected.
    * <https://help.github.com/articles/creating-a-new-repository/>
1. Make a note of your repository URL (HTTPS or SSH, depending on your security configuration).
1. Clone this control repository to your laptop/workstation:
    * `git clone <repository url>`
    * `cd control-repo`
1. Remove this repository as the origin remote:
    * `git remote remove origin`
1. Add your internal repository as the origin remote:
    * `git remote add origin <url of your github repository>`
1. Push the production branch of the repository from your machine up to your git server
    * `git push origin production`

## Code Manager Setup

If you use Puppet Enterprise and have not yet enabled and configured Code Manager, in addition to reading the official [documentation](https://puppet.com/docs/pe/latest/code_management/code_mgr.html) for enabling it, you may want to look at the Ramp-Up Program's control repository instead of this one. It's similar to this repo except that it has batteries included, so to speak. There are pre-built profiles for configuring Code Manager, generating SSH keys, and setting up your Git server to work with Code Manager.

* <https://github.com/Puppet-RampUpProgram/control-repo>

#####################################################################################################################
COPIED FROM : https://github.com/Zymergen/puppet-control-repo-npwalker
Before Starting:
This control repo and the steps below are intended to be used during a new installation of PE.

If you intend to use it on an existing installation then be warned that if you've already written or downloaded modules when you start using r10k it will remove all of the existing modules and replace them with what you define in your Puppetfile. Please copy or move your existing modules to another directory to ensure you do not lose any work you've already started.

Setup a Trusted Fact On Your PE Master
This control repository is setup to manage certain portions of your PE installation for you if you create a trusted fact called pp_role. In order to do so, lay down a file that looks exactly like the below in /etc/puppetlabs/puppet/csr_attributes.yaml

---
extension_requests:
  #pp_role
  1.3.6.1.4.1.34380.1.1.13: 'all_in_one_pe'
If You Have Not Installed PE
Good then you can proceed forward and the trusted fact will be used when you get to the install step.

If You Have Already Installed PE
Trusted facts are created at the time a CSR is generated. So, we need to regenerate the certificate on the master for the above trusted fact to be created.

Follow this document to regenerate the certificate on your master.

http://docs.puppetlabs.com/pe/latest/regenerate_certs_master.html

##Copy This Repo Into Your Own Git Server

###Gitlab

Install Gitlab
https://about.gitlab.com/downloads/
After Gitlab is installed you may sign if with the root user and password 5iveL!fe

Make an user for yourself

Make an ssh key to link with your user. You’ll want to do this on the machine you intend to edit code from ( most likely not your puppet master but your local workstation / laptop )

http://doc.gitlab.com/ce/ssh/README.html
https://help.github.com/articles/generating-ssh-keys/
Create a group called puppet ( this is case sensitive )
http://doc.gitlab.com/ce/workflow/groups.html
Create a user called r10k_api_user and add them to the puppet group
From the landing page, select groups
Choose the puppet group
In the left hand pane, select memembers
Add the r10k_api_user with master permissions
Add your user to the puppet group as well

Create a project called control-repo and set the Namespace to be the puppet group

Logout of root and login as the r10k_api_user

Go to profile settings -> account ( https://<your_gitlab_server>/profile/account )
Copy the api token
Clone this control repository to your laptop/workstation
git clone https://github.com/npwalker/control-repo.git
cd control-repo
git mv hieradata/nodes/example-puppet-master.yaml hieradata/nodes/<fqdn_of_your_puppet_master>.yaml
Open hieradata/nodes/<fqdn_of_your_puppet_master>.yaml
edit gms_api_token to be your api token
edit git_management_system to be 'gitlab'
edit the gms_server_url
git add hieradata/nodes/<fqdn_of_your_puppet_master>.yaml

git commit -m "renaming example-puppet-master.yaml"

Rename my repository as the upstream remote

git remote rename origin upstream
Add your internal repository as the origin remote
git remote add origin <url of your gitlab repository>
Push the production branch of the repository from your machine up to your git server
git push origin production
git branch --set-upstream-to origin/production
###Stash

Coming soon!

###Github

Coming soon!

##Configure PE to Use the Control-Repo

###Install PE

Download the latest version of the PE installer for your platform and copy it to your master
https://puppetlabs.com/download-puppet-enterprise
Expand the tarball and cd into the directory
Run puppet-enterprise-installer to install
If you run into any issues or have more questions about the installer you can see our docs here:

http://docs.puppetlabs.com/pe/latest/install_basic.html

###Get the Control-Repo Deployed On Your Master

At this point you have my control-repo code deployed into your git server. However, we have one final challenge getting that code onto your puppet master. In the end state the master will pull code from the git server via r10k, however, at this moment your puppet master doesn't have credentials to get code from the git server.

So, we'll set up a deploy key in the git server that will allow a ssh-key we make to deploy the code and configure everything else.

On your puppet master, make an ssh key for r10k to connect to gitlab
/usr/bin/ssh-keygen -t rsa -b 2048 -C 'r10k' -f /root/.ssh/r10k_rsa -q -N ''
http://doc.gitlab.com/ce/ssh/README.html
https://help.github.com/articles/generating-ssh-keys/
Create a deploy key on the control-repo project in Gitlab
Paste in the public key from above
cat /root/.ssh/r10k_rsa.pub
Follow https://docs.puppetlabs.com/pe/latest/r10k_config_console.html
The remote is on the front page of the project in the gitlab UI
git_settings should be:
{"provider": "rugged", "private_key": "/root/.ssh/r10k_rsa"}
Run puppet agent -t
Expect to see changes to r10k.yaml
Run r10k deploy environment -pv
Run puppet agent -t
Now you should see many more changes
Test The Zack/r10k Webhook
One of the components setup by this control-repo is that when you "push" code to your git server, the git server will inform the puppet master to run r10k deploy environment -p.

Edit README.md
Just add something to it
git add README.md
git commit -m "edit README"
git push origin production
Allow the push to complete and then give it few seconds to complete
Open /etc/puppetlabs/code/environments/production/README.md and confirm your change is present
#Miscellaneous

If You Want to Install Pointing To This Repo on Github
Setting Up Gitlab
Install Gitlab on a server by specifying the following trusted fact on the soon-to-be Gitlab server and then install the PE agent.
---
extension_requests:
   #pp_role
   1.3.6.1.4.1.34380.1.1.13: 'gitlab'
Setting up Github
Not yet completed.

Setting up Stash
Not yet completed.

#TODO Flush out generating an answer file and then appending extra answers onto the end of it.
