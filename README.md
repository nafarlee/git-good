# Git

If you have no idea what Git or version control are, continue reading.

If you are already comfortable with Git, but are curious how it actually works, skip to "Design and Inner Workings".

If you are already comfortable with Git, but just want to learn some new sweet commands, skip to "Useful Git commands".

## What is Git?

In the simplest terms, Git is a version control system (VCS).
Historically, the purpose of a VCS is exclusively to track changes to files over time.

Though this does not sound like a particularly amazing feat, it is a hard process to get right if you are trying to do it manually.

Manually saving entire copies of a project is incredibly costly on disk space, which puts a premium on backups.
Not to mention renaming copies of folders
from
`cool-project-v2-2016-05-02-10:15 (1)`
to
`cool-project-v2-2016-05-02-10:32`
can get pretty tedious.

Alternatively, to save on disk space, you can copy only the files that changed into these successive folder copies, but then actually viewing the project in full at any given point becomes incredibly difficult.

VCS seeks to alleviate this by offering simple abstractions on top of this project diff business that work at scale.

Someone who is not familiar with VCS might assume this is only useful for a project wide 'undo history'.
However, in practice, using VCS, and Git successfully means treating it much more like a supportive function than just an undo button.

## What are the benefits?

Though not exhaustive, the following list of tangible benefits should (hopefully) spark your interest:

- Extensive project versioning that:
  - Allows viewing the state of a project at any point in time easily
  - Doesn't use unnecessary disk space for full project clones.
- Intelligent tools and supporting capabilities that are possible because of the management of project history.
- Implicit documentation of each change that happens to a project, which shows the path of implementation the developer took to complete a feature/fix an issue.
- Low risk experimentation with single/multiple files, solo or collaboratively, as changes can be selectively undone or left unapplied.
- Collaboration across multiple people can happen asynchronously without:
  - Race conditions when writing to the same file on a central server.
  - Drastic file differences when team members work in isolation.
  - Different development intents can be worked on in parallel.
- Basic backup happens as part of the workflow, and extending to bulletproof backups is simpler
- Software versioning, either as part of release cycles, or maintaining variants, is simplified.

## Evangelism?

While this guide focuses on Git, as it is the VCS I use most often because I am quite happy with it, Git is not a be all end all.
There are other systems that perform similarly to Git, and have different tradeoffs that might make more sense depending upon your use case.

The important thing is to use a true VCS of __some__ kind.
Even if you are developing/writing solo, the utility a VCS provides over manual backups (or worse, nothing at all!) cannot be overstated.

Beyond this point, things become increasingly Git focused.

_You have been warned..._

## Design and Inner Workings

### The Cathedral and the Bazaar

All VCS help track versions of files as people modify them.
In this sense, Git is no different.

Though each VCS might implement different nifty features and workflows, the basic idea is the same.

However, because people like to put things into categories, VCS systems are usually divided into two main genres:

- Distributed
- Centralized

#### Centralized

Centralized VCS function by maintaining a single master copy of a project somewhere (probably a server).

As a team member working on a project using Centralized VCS, you directly attempt to apply patches to a server.
Anytime you get a file, you are getting the most recent version that the server yields.

This means that a developer's machine has little to no record of the actual project history, and all project affecting actions require a networked connection to this centralized server.

Though an extensive pro and con list is outside of the scope of this document, I will remark that this architecture is probably the one that is the most intuitive to someone seeking to implement a VCS, at least at first.
As in, someone interacting with a VCS, not knowing much better, would probably assume this is how all interactions take place.

Examples of Centralized VCS include: SVN, CVS, and Perforce.

#### Decentralized

Decentralized VCS (DVCS) changes one important detail about this model.
There is no central server in DVCS.
There can be a server that holds onto the project and its history and all of its metadata, but the server does not have any special functionality or privileges over any other user.

Each and every user maintains the exact same information that this server holds, and as such, every local machine could function as a server and a client.
In a DVCS, you are simply modifying your local project, recording what has changed, and then sending a list of patches to another client.

If you were so inclined, two or more people could have a local copy of a project, make changes, and then directly send their changes to each and every other developer machine.
All of the mechanics of patch-exchanges would work identically.

In practice, this is not how people utilize DVCS, and still have a central host that is semantically treated as a "master".
However, should anything happen to this master, or should a network connection not be available, the changes a developer makes to their local project are entirely unhindered.

Once the server becomes available again, they can simply send along all of the patches they created in the meantime to the server.
Afterwards, each developer is free to grab these patches from the server, and add them to their local clone of the project.

However, this model has its issues as well, and is made possible by a lot of fairly sophisticated diffing. Usually this is entirely automatic, but sometimes, it can be painfully manual.

Again, measuring the pros and cons of each VCS type is not within the scope of this document, but I personally believe DVCS is an improvement over Centralized VCS.

If you have not guessed yet, Git falls into the category of DVCS.

Examples of Decentralized VCS include: Git, Mercurial, BitKeeper.

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

The working directory, for all intent and purposes, is how your project exactly exists on your filesystem. You interact with these files how you normally would. You may edit, delete, move, and copy with any tools you see fit. If a file only exists in the working directory, and git has never tracked changes for the file, it is referred to as "Untracked".

Once you are happy with the state of a file, or many files, or even a few lines that were changed in a file, you run a git command (`add` in this case) to make the staging area aware of these changes. Imagine the staging area as a transport platform with a box on it, waiting to have references to file changes added in to it.

After we have made the staging area aware of all the file changes we plan to package together, we must now bundle up the box, and send it to the .git directory (which models the repository history).

To do this, we attach a message to this bundle, briefly describing the intent of these changes, and then we `commit` this bundle to the permanent project history.

The entire staging area is sent at once to the .git directory, but you may selectively choose what changes in the working directory are made known to the staging area at any given time. This is how you may finely manage what exactly one `commit` encompasses.

Once changes are commited, they are solidified as a new snapshot in Git's representation of the project history.

All the data Git manages is generated as part of this process. Every other supporting functionality Git offers is just tooling around this information.

### The push/pull Lifecycle

#### Remote

So you have your git repository coming along nicely, making steady progress on your project, but now someone else wants to collaborate with you. Sure, git will manage changes efficiently as snapshots, but if collaborating means copying the project around on flash drives, that all goes out the window anyway.

This is where the push/pull lifecycle comes in, and honestly it is a very simple concept. Though each developer/writer has a copy of the project locally, there is usually a centrally stored version of the project on a server somewhere as well.

So imagine a developer is working on a project alone, and has a lot of progress, but now wants to collaborate. What they will do is set the Git server's endpoint as their remote point (this remote point is set automatically if the project was cloned).

This remote (if there is only one) is usually referred to as origin.

#### Push

Then, they will run `git push origin master`.

The `master` business will be explained later, but this command tells `git` to `push` all of the information about the project in my local .git repository to the `origin` (usually over the network). This is an efficient diff, as it will only send what the server doesn't know already each time.

Keep in mind this is only pushing the .git repositories knowledge, so anything currently in staging or the working directory only won't be a part of the push.

#### Pull

If some other developer/writer also had a local copy of that project on their machine, they could then run `git pull origin master`, which is (sort of) the opposite to `git push origin master`.

Ignorning the `master` stuff again, this command tells `git` to `pull` the .git repository information from `origin`.

The reason this is "sort of" the opposite of the `push` is that is actually gets the known changes from the .git repository on the server, but then also applies them to the working directory.

`git fetch` is the actual opposite of `git push`, as it will only download the .git repository information and apply it to your own .git repository.

What happens if you have changes pending in your working directory and you try to `pull`? Well nothing, actually. It won't allow you to pull until your working directory contains no new changes.

## Useful Git Commands

--------------------------------------------------------------------------------

### `git config`

This is the command that allows you to customize your git configuration. Configurations can be applied globally, which will reside in a config file in your home directory, or locally, which will be stored with the project's `.git` repository.

There are far too many configurations to enumerate, but some useful ones are:

#### `git config --global user.name "John Smith"`

Tell `git` to `config` our git settings `--global`ly by changing our `user.name` for further commits to `John Smith`

#### `git config --global user.email "coolguy@email.com"`

Tell `git` to `config` our git settings `--global`ly by changing our `user.email` for further commits to `coolguy@email.com`

--------------------------------------------------------------------------------

### `git init` and `git clone`

These commands might seem like strange candidates to be lumped together, but they are really two sides to the same coin. Both are run once when you initially want to start working on a project.

`init` is run when the very first person on a project decides they want git in their project's life. This can be before any code is written, or later down the line when you already have some code.

`clone` is run everytime someone wants the project (including absolutely ALL Git project information) to exist on their computer, allowing them to contribute just like any other person.

Typically one person runs the `init` command once at the start of the project, and then whenever a new developer is interested in contributing, they `clone` that same project and then forever have that project.

#### `git init`

Tell `git` to `init`ialize the current directory to be managed by git. Ultimately, this just created the .git folder and all its necessary contents.

#### `git clone <URL>`

Tell `git` to grab the Git project metadata hosted at the URL, and make a `clone` of the project under the current working directory.

--------------------------------------------------------------------------------

### `git add`

This command is the way that a change in the working directory is added to the staging area. It can be run as many times as necessary to add more and more to the staging area, until the staging area contains all wanted changes for the next commit.

The files this acts upon must actually physically reside within the projects folder, either in the root or in a subfolder.

Some useful forms of this command:

#### `git add <filepath>`

Tell `git` to `add` all changes in the file (or folder) at `filepath` to the staging area.

#### `git add -p`

Tell `git` to interactively ask about individual `-p`atch changes to `add` to the staging area. This command is actually quite robust, and not immediately intuitive.

While in the interactive session, a list of diffs will be presented for each changed line in the project.

For each diff, you have a few available commands:

- y - add these changed lines to the staging area
- n - do not add these changes lines to the staging area
- q - leave the interactive patch session
- s - split the currently presented lines, so they may be added or ignored with finer granularity

There are other options, but they are not crucial to the use of the `-p`atch interactive mode.

--------------------------------------------------------------------------------

### `git commit`

This command is the way that a series of changes made know to the staging area are packaged up as a single commit and added to the list of snapshots in the .git repository.

#### `git commit -m "<description>"`

Tell `git` to bundle up the contents of the staging area as a single `commit` with the `-m`essage `<description>`.

--------------------------------------------------------------------------------

### `git status`

Tell `git` to give the `status` of the current branch name and outstanding changes in the working directory or staging area.

You are going to be running this one a lot. Git manages a whole lot of information, and it can be quite confusing moving multiple changes throughout three tiers and remotes and such.

This command prints out a summary of outstanding changes about the working directory and staging area, as well as some other relevant information, like branch name.
