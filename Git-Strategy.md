# Git Strategy

Having a clear strategy of how we use version control at Mentor will:

- Reduce common pain points when collaborating with team members on the same project.
- Create a reliable and auditable history of projects.
- Increase transpencry and encourage knowledge sharing.

---

# Commits

A Commit should contain one small, logically grouped set of modifications to single or multiple files that achieve one primary goal.

## Message

A good Commit message starts with a capital letter, is less than 80 characters and accurately summarises the **action** that will be applied to the repository.

Example Scenario:

You have modified the `.gitignore` to ignore Visual Studio 2019 generated files.

Helpful: 
- `Ignore Visual Studio 2019 generated files`

Bad: 
- `updated gitignore`
- `Adds .vs to gitignore`

## Preparation / Staging your changes

If you have the opportunity, stage and commit your changes as you go - This will break down the git work into smaller, more manageable c**hunks**, This will also give you the added benefit of version control before you've pushed to the remote.

Useful commands:

### Stage files with `--patch`

- `git add -p <file>`
- `git add --patch <file>`

Iterate over each *hunk* (change) and decide whether to stage it or not.

### Unstage files with `--patch`

- `git reset -p <file>`
- `git reset --patch <file>`

Iterate over each *hunk* (change) and decide whether to **un**stage it or not.

---

# Branches

## Master Branch

Apart from the initial Commit, the `master` branch should never be directly pushed to.

It should only ever be modified after a [Release](#release-branches) branch has had a successful [Merge Review](#merge-reviews).

### Intial Commit

At a minimum, the initial Commit should contain:

- `Readme.md`, explaining the project's name and goal.
- `.gitignore`, ignoring IDE generated files, binaries and all other files that do not require version control. 

## Release Branches

A Release Branch should be created as soon as a repository requires functionality.

Similar to the `master` branch, a release branch should never be directly pushed to and only modified after a successful [Feature](#feature-brances) branch has had a successful [Merge Review](#merge-reviews).

## Naming

Releases are to be named `release/x.x.x` where `x.x.x` coralates to `Major.Minor.Patch` version numbers.

Example: `release/0.2.2`

See [Semantic Versioning](#semantic-versioning)

## Feature Branches

A Feature Branch is where *all* of your commits will live.

Usually, a feature branch will have only one active developer, this prevents any gridlock.
If a developer requires your current changes that are not yet merged, see [Rebasing](#rebasing).

After you have successfully implemented a new feature, submit a [Merge Review](#merge-reviews).

### Naming

Feature branches should be lowercase with dash separated words.

Example: `add-two-factor-authentication`

---

# Merge Reviews

A Merge Review is the perfect place for knowledge sharing, discussion, and feedback relating to your work.

## Particpants

A review has at minimum two parties, an author and a reviewer.

### The Reviewer(s)

It is a reviewer's responsibility to ensure:

- Documentation strings are well defined. (XML Comments)
- The code is structured in a readable, maintainable manner.
- Relevant changes to Readme / Documentation have been made.
- The spelling is correct.
- Naming is accurate and consistent.
- **Merging**, Once all issues have been resolved and the above criteria met.

If any of these requirements are not met, the author should leave a comment referencing the exact issue.

### The Author(s)

Is it an author's responsibility to:

- Write a summary of the included changes in the Merge Request description.
- Assign an appropriate Reviewer.
- Make / Contest requested changes.
- Respond to all critique and resolve all concerns.

### All comments must be constructive, neutral and relevant to the proposed change.

## Merging Releases

When conducting a review of a [Release](#release-branches) the Reviewer has an important task to complete before merging - [Tagging](#tagging-a-release).

---

# Releases

## Tagging a Release

## Semantic Versioning
