## Strategy

Git Flow: We use a our own version of the gitflow strategy where we have the following branches:

| Branch  | Parent branch | Merges into | Purpose                                                                                                                                                              |
| ------- | ------------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| main    | n/a           | development | The main branch is where the production code exists and generates new releases upon development PR merges to main                                                    |
| develop | main          | main        | The development branch is used to integrate finished feature branches and acts as a buffer in front of main, to catch failures when combining multiple new features. |
| feature | development   | development | Feature branches are used for developing new system features and testing them before integrating them into the development branch                                    |
| fix     | development   | development | Fix branches are used when changes are to be made, but no know features are added.                                                                                   |
**main branch releases and tag versioning**
On our main branch we use github actions to automate release when merging from the development branches.

We decided to automate this process to unsure our main is always updated with releases when new code is merged. This works because on a successful PR merge to main the PR title has to contain the release type. Our github actions then runs a workflows yaml file that gets the current release version and increments the tag appropriately, then it makes a new releause with the github cli tool.
## Enforcement

To enforce the branching strategy we have set up rulesets for main and development on our repository. 

**main branch ruleset:**
* Restrict deletions
* Requires PR before merging
* Requires status check to pass
	* A yaml file runs that checks the PR title, because it has to contain a release type like major:, minor:, fix:, as the first part of the title like this "major: All endpoints finished"
* Block force pushes

**development branch ruleset:**
* Restrict deletions
* Require PR before merging
* Block force pushes

## Why we chose Gitflow

We chose Gitflow because we like the way its separates branches by functionality. By dividing branches by single responsibilities we can better unsure that no branch is held back by differing workloads and we call all work on different branches in parallel. The main and development branches are key to us always having a releasable version of the project while also not contaminating our release version with possible bug introducing features. 

## Other strategies
The other branching strategies that use less layers could be faster and more simple when moving from feature to release. We did not plan on making many tests from the beginning and that meant a strategy like trunk based with quick merges from features branches to main could lead to each release introducing a lot of bugs.
We could then consider using Github flows but again the lack of a buffer would mean that we would have to be more careful going through each PR and discussing the code changes in depth to keep bug introduction at a minimal. Instead with Gitflows we can catch bugs in the dev branch and we dont have to rely as much on tests and rigorous rewiev and discussion of each feature integration. This also frees each group member up to work on more tasks in parallel instead.


## Gitflows pros and cons

| Pros                                                                                                        | Cons                                                                                                           |
| ----------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| PR made it more clear group wide when changes were boing made to the code                                   | It does take a bit of extra time to both make and review PR from feature branch to dev, and from dev to main.  |
| We could be more confident about merging features into dev since we would always have main to fall back on. | With all the rules and layers we have chosen to follow it did take some time to get used to the flow of PR.    |
| main mostly stayed stable at all times                                                                      | PR could be slow to be reviewed when most group members were unavailable or preoccupied.                       |
|                                                                                                             | We did have some merge conflicts and when resolving them we accidentally removed other group members features. |
