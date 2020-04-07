# Gitlab Flow

_GitLab Flow, filling in the blanks_

## Official Documentation

- [Introduction to GitLab Flow](https://docs.gitlab.com/ee/topics/gitlab_flow.html)
- [What is the GitLab Flow](https://docs.gitlab.com/ee/university/training/gitlab_flow.html)
- [The 11 Rules of GitLab Flow](https://about.gitlab.com/blog/2016/07/27/the-11-rules-of-gitlab-flow/)

## Notes

Paraphrasing From the [Official Documentation](https://docs.gitlab.com/ee/topics/gitlab_flow.html):

> hotfix and release branches can be a good idea for some organizations but are overkill for many. Nowadays, most organizations practice continuous delivery, which means that your default branch can be deployed. Continuous delivery removes the need for hotfix and release branches

Despite these comments, the [Introduction to GitLab Flow](https://docs.gitlab.com/ee/topics/gitlab_flow.html) goes on to discuss stragies involving release branches and hotfixes.

## Clarifications

### Without Release Branches

Assuming we follow the GitLab flow advice that hotfix and release branches are not needed, how does the flow work?

1. `master` is the main working branch
1. When ready to initiate a relase, create a tag in `master`
1. Create a release artifact from that tag
1. Deploy and test the release artifact, and promote it up through the environments
1. Once ready, deploy to production
1. Merge from `master` into `production` at the point of the release tag

If you need to hotfix, and following the steps above using `master`'s `HEAD` (i.e. releasing any new commits which have been added since the last release tag) is not an option:

1. Create a new hotfix branch from the previous release tag in `master`
1. Apply the hotfix to `master`
1. `cherry-pick` the hotfix onto the new hotfix branch
1. Create a release tag in the hotfix branch
1. Create a release artifact from hotfix branch
1. Deploy, test, and promote up through the environments
1. Deploy to production
1. Merge the hotfix branch into `production`

One big downside to the above: the release tag won't exist in `master` like all the other release tags. I don't think the tag will exist in `production` after merging either.

Also, the same procedure would have to be followed if there was a bugfix needed for the release code and additional commits were made to `master` since the relase tag, and including those new commits in the relase is not an option. Even if it is an option, this could get ugly if bugs keep getting discovered in the release code, and more and more commits keep getting piled into the release.


### Release Branches

- Branch from master as late as possible
- Only changes to release branch are critical bug fixes which should first be applied to master then cherry-picked to the release branch
- Release branches can be git tagged, e.g. `1.2.3` and kept around for a period of time
  - Open question: then how do tags get back into `master` so that they can be kept indefinitely, i.e. when the release branch gets deleted. I don't think the tag will exist in `production` after merging either.
- Release branches contain the code which is deployed and promoted up through the environments
- Assuming changes to `production` branch aren't deployed to production automatically, once the release artifact is released to production, the release branch is manually merged into the `production` branch

### Hotfixes

- Apply hotfix to master
- Create a branch off of the last release branch
- Then cherry pick from master onto the new release branch
- Tag the release branch


## Questions

- How do tags for hotfix branches get saved?
- How do hotfix artifacts get created?


## Other Resources

- Discussion of confusion caused by [Introduction to GitLab Flow](https://docs.gitlab.com/ee/topics/gitlab_flow.html): https://stackoverflow.com/questions/38395497/hotfix-on-gitlab


