# Git Flow
​
## Install laravel locally ( repository admin )
​
```bash
install-laravel-7 miniature-octo-memory
cd miniature-octo-memory
git init
git add .
git commit -m"Inital Commit Laravel Install"
```
​
### Create a repo on github
visit your github account and create a new repo called miniature-octo-memory
​
### Push existing repo 
​
```
git remote add origin https://github.com/fbhood/miniature-octo-memory.git
git branch -M main
git push -u origin main
```
​
Note: if using two accounts on the same machine you can change the local configuration for the current repo by running
​
```
​
git config --local credential.username your_user_name_here
```
this way the current repo will use the credential specified in the .git/config file present in your local repository instead of the glocal system configuration.
readmore [here](https://git-scm.com/docs/gitcredentials)
​
​
### Invite collaborators (repo admin)
visit the repository page then settings > manage access 
click the add people button and type the username of the person you want to add.
​
​
## Collaborate (repo collaborator) 
​
### Accept the invite 
Visit the repository page and accept the invite.
​
### Clone the repo ( repository collaborator )
​
click on code and copy the repo url then clone it locally
​
```bash
cd dev/
git clone https://user_name/repo_name.git
```
​
Now we have access to the repo and we can push changes to it.
​
​
​
## Work on the repository (collaborator)
To work with the repo we have just cloned we need to perform a few tasks before we can run laravel 
​
- `composer install`
- `cp .env.example .env`
- `php artisan key:generate`
- `npm install`
- `php artisan serve`
​
Now we can see the laravel welcome page on localhost:8000
and start working.
​
## Create an issue (repo admin)
let's create a new issue for "creating a layout file" 
​
## Work on the issue (repo collaborator) | no code review 
The collaborator will take the issue and start working on it, assign the issue to your self and comment to let the team know you are working on it.
- assign the issue to yourself
- comment to let the team know you are working on it
- create a new branch and start working
​
### Create a new local branch
```bash
git checkout -b 1-layout
```
​
### Create the layout
create a layouts/app.blade.php file and define the layout as usual.
​
make all changes to create the layout than commit.
​
```bash
git add .
git commit -m"add layout file #1"
```
here we can take different approaches depending on how your team decided to work.
​
Let's see the first option that we have
​
### merge to the main brach and push.
With this approach we will merge the 1-layout branch to the main brach and push our changes to the remote repo
​
⚡ no code review will be carried on
​
```bash
git checkout main
git merge 1-layout
git push
```
​
The chage will be applied to the remote repo.
​
## Work on the issue (repo collaborator) | with code review (better!)
​
we will follow the same steps we followed before but this time we will push our branch to the remote and instead of merging directly all changes inside the main branch we will create a pull request.
​
- create a new issue (ie. extend the layout in the welcome view)
- assign the issue to ourself
- create a new branch and start working
- push the branch remotely
- create a pull request
- review the request
- merge to the main branch
​
```bash
# create a branch
git checkout -b 2-exetend-layout-welcome-view
# Edit the welcome view then commit and push
git add .
git commit -m"update welcome view close #2"
git push # this will fail as we need to
# add the current repo to the remote stream 
git push --set-upstream origin 2-welcome-view
```
​
### Create a pull request on github (collaborator)
​
Now when you visit the repo, you will see that there is a new branch and it needs to be merged, to do that we first create a pull-request so that the repo admin can review the changes before merging to the main branch.
​
- Visit the code tab and click on the "compare & pull request" button to create the pull request.
​
- add a comment to describe your work and click "create pull request".
​
### Pull request and Code review (repo admin)
​
visit the pull requests tab and the repository and click the new pull request.
​
click the commit tab to view the changes
​
👍 Assuming that everythig is on click the review changes:
here we can chose three options (comment, approve and request changes) for now let's just approve the pull request and confir.
​
next we can click on "Merge pull request" to update the main branch, we can also choose to :
- create a merge commig
- squash and merge
- rebase and merge
​
let's just create and merge commit. next we can delete the branch either locally and on the remote.
​
```bash
# checkout the main branch
git checkout main
# pull the latest changes
git pull
# delete the branch
git branch -d branch_name_here
```
​
## Dev branch (one step further) | admin
let's push things one step forward and create a dev branch where do all our work
​
click on the code tab and create a dev branch on github.
Now we have a branch that is a copy of the main branch where we can do all the work without affecting the production site (main branch)
​
## Pull the latest changes and switch to the dev branch (collaborator)
​
```bash
# pull changes
git pull
# checkout the development brach
git checkout development
# list branches
git branch
```
​
Now we will ony create new braches out of the development branch and merge them here before crating a pull request to merge all changes to the main branch when we want to make a new release.
​
## Create a new issue and Work on a new feature (collaborator)
- create a new issue for a contact page
- assign it to ourself
- create a branch
- work on the new page
​
```bash
# checkout a new branch
git checkout -b 6-contact-page
```
### create a new contact.blade.php view
```bash
cd resources/views
cp welcome.blade.php contacts.blade.php
```
edit the php file
```php
@extends('layouts.app')
​
@section('content')
    <h1>Contacts Page</h1>
@endsection
```
​
👉 commit our changes
​
### add a new route
​
```php
Route::get('/contacts', function () {
    return view('contacts');
});
```
​
👉 commit our changes and push the rempo to the remote stream
​
​
```bash
git commit -am"add a contacts route close #6"
git push --set-upstream origin 6-contact-page  
```
​
### Create a pull request (collaborator)
click the code tab then "compare and pull request"
⚡ next make sure to change the base to development instead of main so that our changes will be merged against the development branch. 
​
Write a description for the pull request and click  "Create pull request"
​
​
## Merge changes (Admin)
on the pull request tab there is a new request, click on it and follow the same steps we have done previously.
​
- click on the commits tab to review the code and incase ask to apply changes 
​
- select the first commit and click the plus sign next to the `<h1>Contacts page</h1>` and ask to change to "Contacts" only. then press start review.
​
write your changes request then press "finish review" and select "request changes" and submit the review.
​
## Make requested changes and resolve the conversation.
on the "conversation" tab look at the conversation and click on "view Changes"
​
### Reply and resolve the conversation
- on will do the changes
### Make changes and push again.
​
```php
​
@extends('layouts.app')
​
@section('content')
    <h1>Contacts</h1>
@endsection
```
​
commit and Push 
```bash
git commit -am"edit the contact page to replect requested changes close #6"
git push
```
​
​
## Final review (admin)
Approve the changes, merge into development and remove the remote branch 6-xxxx
​
​
## Clean up (collaborator)
```
git checkout development
git pull
git branch -d 6-contact-page
```
​
Now that our development branch is up to date we can work on a new feature!
​
- create a new issue, 
- assign it, 
- create a branch out of the development branch
- start your work
​
​
## Manage conflicts
​
Let's immagine that the repo admin wants to create a new feature and decides to open a new issue, we assign the issue to us but he  decides to start building it before we push our changes. This will create conflicts that we will have to resolve before we can merge.
​
### ADMIN | create the issue, a branch, start working, push and merge
​
### Collaborator | take the issue, create a local branch, work, push and create a pull request.