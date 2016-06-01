# Git

If you are already comfortable with Git, but are partially curious how it actually works, skip to "Design and Inner Workings".

If you are already comfortable with Git, but just want to learn some new sweet commands, skip to "Useful Git commands".

## What is Git?

Many would be inclined to exclusively describe Git as a version control system (VCS), though I personally believe this is selling Git a bit short.

Certainly its main focus, as is the focus of all VCS, is to organize changes made to a project over time.

Someone who is not familiar with VCS might assume this is only useful for a project wide 'undo history'. 

However, in practice, using Git succesfully means treating it much more like a supportive function than just an undo button.

## What are the benefits?

Though not exhaustive, the following list of tangible benefits should (hopefully) spark your interest:

- Intelligent tools and supporting capabilities that are possible because of the management of project history.
- Implicit documentation of each change that happens to a project, which shows the path of implementation the developer took to complete a feature/fix an issue.
- Low risk experimentation with single/multiple files, solo or collaboratively, as changes can be selectively undone or left unapplied.
- Extensive project versioning that:
  - Allows viewing the state of a project at any point in time easily
  - Doesn't use unnecessary disk space for full project clones.
- Collaboration across multiple people can happen asynchronously without:
  - Race conditions when writing to the same file on a central server.
  - Drastic file differences when team members work in isolation.
  - Different development intents can be worked on in parallel. 
- Basic backup happens as part of the workflow, and extending to bulletproof backups is simpler
- Versioning, either as part of release cycles, or maintaing variants, is simplified.

## Design and Inner Workings

We can talk all day about the intricacies and architecture of Git, but there are only a few concepts that are necessary to understand if you want to make good use of Git.

### How Git Exists in a Project

To start, the way git actually manages a project is through a hidden .git folder that exists within the root of the project.

This is the folder that marks the difference between a project managed by Git, and one that does not. The entire project history, and all supporting metadata that exists for that project is within this folder.

As a result, if you have a project managed by Git, and you no longer want to manage it via Git, and do not care about the history Git has managed, simply __deleting the .git folder is all that is necessary to stop using Git and remove all history for that project__.

That sounds scary, but chances are, you will not manually edit anything in the .git folder for the entire duration of your career, and _almost_ all Git commands append new information to the history, rather than change previous history. 

As a result, it is __very hard__ to lose information about your project when you are working with Git.

### The Three Main Git States

During everyday use, a file and associated changes, as managed by Git, can exist in three main states (there are a few more, but they aren't necessary to get started).

The three main states are:

- The Working Directory
- The Staging Area
- The .git directory

![Main Git States](./images/states.png)

The working directory, for all intent and purposes, is how your project exactly exists on your filesystem. You interact with these files how you normally would. You may edit, delete, move, and copy with any tools you see fit.

Once you are happy with the state of a file, or many files, or even a few lines that were changed in a file, you run a git command (`add` in this case) to make the staging area aware of these changes. Imagine the staging area as a transport platform with a box on it, waiting to have references to file changes added on to it.

After we have made the staging area aware of all the file changes we plan to package together, we must now bundle up the box, and send it to the .git directory (which models the repository history). 

To do this, we attach a message to this bundle, briefly describing the intent of these changes, and then we `commit` this bundle to the permanent project history.

The entire staging area is sent at once to the .git directory, but you may selectively choose what changes in the working directory are made known to the staging area at any given time. This is how you may finely manage what exactly one `commit` encompasses.

Once changes are commited, they are solidified as a new snapshot in Git's representation of the project history.

All the data Git manages is generated as part of this process. Every other supporting functionality Git offers is just tooling around this information.

## Useful Git Commands

### `git config`

This is the command that allows you to customize your git configuration. Configurations can be applied globally, which will reside in a config file in your home directory, or locally, which will be stored with the project's `.git` repository.

There are far too many configurations to enumerate, but some useful ones are:

#### `git config --global user.name "John Smith"`

Tell `git` to `config` our git settings `--global`ly by changing our `user.name` for further commits to `John Smith`

#### `git config --global user.email "coolguy@email.com"`

Tell `git` to `config` our git settings `--global`ly by changing our `user.email` for further commits to `coolguy@email.com`
