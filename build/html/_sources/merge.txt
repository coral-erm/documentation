Merging Repositories
--------------------

***Test line***

Project to merge repositories launched in 2015

This page is a work in progress

How to move from multi-modules installs to the coral-erm main repository

The goal is to migrate from Coral with several modules as you know from ndlibersa to a single repository. This procedure is a way to do it. Setup a Coral "nearby" your current coral setup and switch from one to the other.

Pre-requisites

Be sure to be up to date for all Coral modules

For the example I used two locations:

CORAL_MULTI_MODULES=/home/coral/www # current setup CORAL_MAIN_REPO=/home/coral/main # setup for testing and future source code

Checkout new sources

cd $CORAL_MULTI_MODULES cd .. git clone git://github.com/Coral-erm/main.git

Create another site for web server

Clone your current config file and change the source directory

Example with nginx:

cp /etc/nginx/sites-enabled/coral-httpd.conf /etc/nginx/sites-enabled/coral-main-httpd.conf vim /etc/nginx/sites-enabled/coral-main-httpd.conf

Replace CORAL_MULTI_MODULES by CORAL_MAIN_REPO directory in coral-main-httpd.conf and server_name

service nginx stop service nginx start

Copy your current config files in main repository

cd $CORAL_MAIN_REPO cp $CORAL_MULTI_MODULES/auth/admin/configuration.ini $CORAL_MAIN_REPO/auth/admin/configuration.ini cp $CORAL_MULTI_MODULES/resources/admin/configuration.ini $CORAL_MAIN_REPO/resources/admin/configuration.ini cp $CORAL_MULTI_MODULES/organizations/admin/configuration.ini $CORAL_MAIN_REPO/organizations/admin/configuration.ini

[...] Do it for all your modules.

Test your new site pointing on CORAL_MAIN_REPO

Check everything is alright with the new main repository and your databases. Switch sites to have the main repo in the good location

mv $CORAL_MULTI_MODULES old_coral_setup mv $CORAL_MAIN_REPO $CORAL_MULTI_MODULES

Enjoy, the new main repository is now in production. How has been created this main repository

The idea was to move all module codes/patches into a subdirectory with the target name, then merge the result in a single git repository. Example :

the organization repository has files in . (index.php, templates/, admin.php ...)
after the following commands, everything is move in a organizations directory What must be understood is that git clone organizations http://github.com/... create an organizations directory. But it's the "root" directory of the repository. What we want is to have everything in an "organizations directory" that is a subdir of this one. So at the end of the process, the code is in organizations/organizations/*, and that's what we want, because the 1st "organizations" is the repo name.
The magic command is :

git filter-branch --prune-empty --tree-filter 'mkdir -p organizations && git ls-tree --name-only $GIT_COMMIT | xargs -I files mv files organizations'

It create an organizations directory and move all files created/updated by a patch into this directory.

Complete script:

clone main

cd ~/coralmerged git clone git@github.com:ndlibersa/CORAL-Main.git

clone organizations

git clone git@github.com:ndlibersa/organizations.git

enter organizations and move everything into organizations/organizations

cd organizations git filter-branch --prune-empty --tree-filter 'mkdir -p organizations && git ls-tree --name-only $GIT_COMMIT | xargs -I files mv files organizations'

now, go back to CORAL-Main and merge the organizations patches

cd ../CORAL-Main git remote add organizations ~/coralmerged/organizations/ git remote update organizations git merge origin/organizations

that's all folks.

do that for every module

Hey, for resources and reports, I get a nasty message "blabla is not empty".

Yes. That's because for resources & reports, there is already a resources & reports subdirectory (that is quickly dropped for reports, but it exist at one time). So moving into the resources/reports directory is not possible, as it already exist.

We need to trick git by moving into a temporary directory, then rename it. Thus we have:

    resources/cataloguing.php then
    newresources/resources/cataloguing.php then
    resources/resources/cataloguing.php.
    We do this in 4 steps:

move everythin in a newresource directory

git filter-branch --prune-empty --tree-filter 'mkdir -p newresources && git ls-tree --name-only $GIT_COMMIT | xargs -I files mv files newresources'

rename the newresources directory into resources

git filter-branch --prune-empty --tree-filter 'mkdir -p resources && mv newresources/* resources/'

move .htaccess

git filter-branch -f --prune-empty --tree-filter 'mkdir -p resources && mv newresources/.htaccess resources/'

move .gitignore

git filter-branch -f --prune-empty --tree-filter 'mkdir -p resources; if [ -e "newresources/.gitignore" ]; then mv newresources/.gitignore resources/; fi'

(The .htaccess & .gitignore steps are mandatory because the * does not include .* files)

Once this is made for all ndlibersa modules, add coral-main repository, and push the merged repository

git remote add coral-erm git@github.com:Coral-erm/main.git git remote update coral-erm git push -f coral-erm master

Compare two directories (merged repo & non-merged instance with multiple repos to verify everything is OK

To verify directories tree before / after, you can use

diff <(cd directory_before && find | sort) <(cd directory_after && find | sort) > output.log