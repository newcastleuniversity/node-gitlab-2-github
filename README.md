# node-gitlab-2-github

This is a specially-modified version of node-gitlab-2-github that will take issues from your ancient Gitlab on-prem instance that has v3 of the API and put them into GitHub so that you can finally kill your Gitlab on-prem instance. :partying_face:  The branch name *goldencommit* refers to the one commit I found in upstream that would talk to both Gitlab v3 and GitHub v3.  Work done since has been to tweak it to meet my needs:
- Adding a resume bookmark to cope with secondary rate limit restrictions.
- Popping a few sleeps in to get more issues transferred before breaking the secondary rate limits.
- Allowing null due dates on milestones.

## Gotchas

- Your assignees must have accounts in GitHub and have Triage or higher access to the GitHub version of the repo.  Otherwise, GitHub will complain about validation errors.

## Install
1. You need node/iojs and npm installed
1. clone this repo with `git clone https://github.com/piceaTech/node-gitlab-2-github.git`
1. `cd node-gitlab-2-github`
1. `npm i`

## Usage

Before using this script "copy" your gitlab repo with all the branches over to github. For this create a new repo with the infos (github.owner and github.repo) from the settings.json and push all the branches you need in the new repo. (Then will the autolinking of issues to commits work.)

1. `mv sample_settings.json settings.json`
1. edit settings.json
1. run `node index.js`


## Where to find info for the settings.json


### gitlab

#### gitlab.url

The URL under which your gitlab instance is hosted.

#### gitlab.token

Go to your settings. Open the account tab. The private Token is the token needed.

#### gitlab.projectID

Leave it null for the first run of the script. Then the script will show you which projects there are.

#### gitlab.startFrom

This should be 1 most of the time.  If you find yourself getting errors from GitHub about hitting secondary rate limits, use this to resume from the correct issue.  Example, when I was importing over 100 issues, I would get secondary rate limit errors when creating issue 42.  Issue 41 was the last successfully-created issue, so I put 42 in gitlab.startFrom and waited an hour before resuming.

### github

#### github.url

Where is the github instance hosted? Default is the official api.github.com domain

#### github.pathPrefix

Only needed when using github enterprise and not beeing hosted at the root of the domain

#### github.owner

Under which organisation or user will the new project be hosted


#### github.repo

What is the name of the new repo

### usermap

Maps the usernames from gitlab to github. If the assinee of the gitlab issue is equal to the one currently logged in github it will also get assigned without a usermap. The Mentions in issues will also be translated to the new github name.

### projectmap

When one renames the project while transfering so that the projects don't loose there links to the mentioned issues.


## Import limit
Because Github has a limit of 5000 Api requests per hour one has to watch out that one doesn't get over this limit. I transfered one of my project with it ~ 300 issues with ~ 200 notes. This totals to some 500 objects excluding commits which are imported through githubs importer. I never got under 3800 remaining requests (while testing it two times in one hour).

So the rule of thumb should be that one can import a repo with ~ 2500 issues without a problem.

## Bugs

the milestone refs and issue refs do not seem to be rewritten properly at the
moment. specifically, milestones show up like `%4` in comments
and issue refs like `#42` do not remap to the `#42` from gitlab under the new
issue number in github. @ references are remapped properly (yay). If this is a
deal breaker, a large amount of the code to do this has been written it just
appears to no longer work in current form :(
