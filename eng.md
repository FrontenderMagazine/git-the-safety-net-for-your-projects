I remember January 10, 2010, rather well: it was the day we lost a project’s
complete history. We were using Subversion as our version control system, which 
kept the project’s history in a central repository on a server. And we were 
backing up this server on a regular basis—at least, we thought we were. The 
server broke down, and then the backup failed. Our project wasn’t completely 
lost, but all the historic versions were gone.<aside class="content-minutiae
">

## Illustration:

## Share This:</aside>

Shortly after the server broke down, we switched to Git. I had always seen
version control as torturous; it was too complex and not useful enough for me to
see its value, though I used it as a matter of duty. But once we’d spent some 
time on the new system, and I began to understand just how helpful Git could be.
Since then, it has saved my neck in many situations.

During the course of this article, I’ll walk through how Git can help you
avoid mistakes—and how to recover if they’ve already happened.

## Every teammate is a backup

Since Git is a distributed version control system, every member of our team
that has a project cloned (or “checked out,” if you’re coming from Subversion) 
automatically has a backup on his or her disk. This backup contains the latest 
version of the project, as well as its complete history.

This means that should a developer’s local machine or even our central server
ever break down again (and the backup not work for any reason), we’re up and 
running again in minutes: any local repository from a teammate’s disk is all we 
need to get a fully functional replacement.

## Branches keep separate things separate

When my more technical colleagues told me about how “cool” branching in Git
was, I wasn’t bursting with joy right away. First, I have to admit that I didn’t
really understand the advantages of branching. And second, coming from 
Subversion, I vividly remembered it being a complex and error-prone procedure. 
With some bad memories, I was anxious about working with branches and therefore 
tried to avoid it whenever I could.

It took me quite a while to understand that branching and merging work
completely differently in Git than in most other systems—especially regarding 
its ease of use! So if you learned the concept of branches from another version 
control system (like Subversion), I recommend you forget your prior knowledge 
and start fresh. Let’s start by understanding why branches are so important in 
the first place.

### Why branches are essential

Back in the days when I *didn’t* use branches, working on a new feature was a
mess. Essentially, I had the choice between two equally bad workflows:

(a) I already knew that creating small, granular commits with only a few
changes was a good version control habit. However, if I did this while 
developing a new feature, every commit would mingle my half-done feature with 
the main code base until I was done. It wasn’t very pleasant for my teammates to
have my unfinished feature introduce bugs into the project.

(b) To avoid getting my work-in-progress mixed up with other topics (from
colleagues or myself), I’d work on a feature in my separate space. I would 
create a copy of the project folder that I could work with quietly—and only 
commit my feature once it was complete. But committing my changes only at the 
end produced a single, giant, bloated commit that contained all the changes. 
Neither my teammates nor I could understand what exactly had happened in this 
commit when looking at it later.

I slowly understood that I had to make myself familiar with branches if I
wanted to improve my coding.

### Working in contexts

Any project has multiple contexts where work happens; each feature, bug fix,
experiment, or alternative of your product is actually a context of its own. It 
can be seen as its own “topic,” clearly separated from other topics.

If you don’t separate these topics from each other with branching, you will
inevitably increase the risk of problems. Mixing different topics in the same 
context:

*   makes it hard to keep an overview—and with a lot of topics, it becomes
    almost impossible;
   
*   makes it hard to undo something that proved to contain a bug, because it’
    s already mingled with so much other stuff;
   
*   doesn’t encourage people to experiment and try things out, because they
    ’ll have a hard time getting experimental code out of the repository once it’s 
    mixed with stable code.
   

Using branches gave me the confidence that I couldn’t mess up. In case things
went wrong, I could always go back, undo, start fresh, or switch contexts.

### Branching basics

Branching in Git actually only involves a handful of commands. Let’s look at
a basic workflow to get you started.

To create a new branch based on your current state, all you have to do is pick
a name and execute a single command on your command line. We’ll assume we want 
to start working on a new version of our contact form, and therefore create a 
new branch called “contact-form
”:

    $ git branch contact-form
    

Using the `git branch` command without a name specified will list all of the
branches we currently have (and the “-v” flag provides us with a little more 
data than usual
):

    $ git branch -v
    <figure>

![Git screen showing the current branches of contact-form.][1]</figure>
You might notice the little asterisk on the branch named “master.” This
means it’s the currently active branch. So, before we start working on our 
contact form, we need to make this our active context:

    $ git checkout contact-form
    

Git has now made this branch our current working context. (In Git lingo, this
is called the “HEAD branch”). All the changes and every commit that we make from
now on will only affect this single context—other contexts will remain untouched.
If we want to switch the context to a different branch, we’ll simply use the
`git checkout` command again.

In case we want to integrate changes from one branch into another, we can “
merge” them into the current working context. Imagine we’ve worked on our “
contact-form” feature for a while, and now want to integrate these changes into 
our “master” branch. All we have to do is switch back to this branch and call 
git merge:

    $ git checkout master
    $ git merge contact-form
    

### Using branches

I would strongly suggest that you use branches extensively in your day-to-day
workflow. Branches are one of the core concepts that Git was built around. They 
are extremely cheap and easy to create, and simple to manage—and there are
[plenty of resources][2] out there if you’re ready to learn more about using
them.

## Undoing things

There’s one thing that I’ve learned as a programmer over the years:
mistakes happen, no matter how experienced people are. You can’t avoid them, but
you can have tools at hand that help you recover from them.

One of Git’s greatest features is that you can undo almost anything. This
gives me the confidence to try out things without fear—because, so far, I haven’
t managed to*really* break something beyond recovery.

### Amending the last commit

Even if you craft your commits very carefully, it’s all too easy to forget
adding a change or mistype the message. With the`—amend` flag of the 
`git commit` command, Git allows you to change the *very last* commit, and it
’s a very simple fix to execute. For example, if you forgot to add a certain 
change and also made a typo in the commit subject, you can easily correct this:

    $ git add <b>some/changed/files</b>
    $ git commit --amend -m "The message, this time without typos"
    

There’s only one thing you should keep in mind: you should never amend a
commit that has already been pushed to a remote repository. Respecting this rule,
the “amend” option is a great little helper to fix the last commit.

(For more detail about the `amend` option, I recommend Nick Quaranto’s 
[excellent walkthrough][3].)

### Undoing local changes

Changes that haven’t been committed are called “local.” All the
modifications that are currently present in your working directory are “local” 
uncommitted changes.

Discarding these changes can make sense when your current work is… well…
worse than what you had before. With Git, you can easily undo local changes and 
start over with the last committed version of your project.

If it’s only a single file that you want to restore, you can use the 
`git checkout` command:

    $ git checkout -- <b>file/to/restore</b>
    

Don’t confuse this use of the `checkout` command with switching branches (see
above). If you use it with two dashes and (separated with a space!) the path to 
a file, it will discard the uncommitted changes in a given file.

On a bad day, however, you might even want to discard all your local changes
and restore the complete project:

    $ git reset --hard HEAD
    

This will replace all of the files in your working directory with the last
committed revision. Just as with using the checkout command above, this will 
discard the local changes.

Be careful with these operations: since local changes haven’t been checked
into the repository, there is no way to get them back once they are discarded!

### Undoing committed changes

Of course, undoing things is not limited to local changes. You can also undo
certain commits when necessary—for example, if you’ve introduced a bug.

Basically, there are two main commands to undo a commit:

#### (a) git reset<figure>

![Illustration showing how the `git reset` command works.][4]</figure>
The `git reset` command really turns back time. You tell it which version you
want to return to and it restores exactly this state—undoing all the changes 
that happened after this point in time. Just provide it with the hash ID of the 
commit you want to return to:

    $ git reset -- hard 2be18d9
    

The `—hard` option is the easiest and cleanest approach, but it also wipes away
all local changes that you might still have in your working directory. So, 
before doing this, make sure there aren’t any local changes you’ve set your 
heart on.

#### (b) git revert<figure>

![Illustration showing how the `git revert` command works.][5]</figure>
The `git revert` command is used in a different scenario. Imagine you have a
commit that you don’t want anymore—but the commits that came afterwards still 
make sense to you. In that case, you wouldn’t use the`git reset` command
because it would undo all those later commits, too!

The `revert` command, however, only reverts the *effects* of a certain commit.
It doesn’t remove any commits, like`git reset` does. Instead, it even creates a
*new* commit; this new commit introduces changes that are just the opposite of
the commit to be reverted. For example, if you deleted a certain line of code,
`revert` will create a new commit that introduces exactly this line, again.

To use it, simply provide it with the hash ID of the commit you want reverted
:

    $ git revert 2be18d9
    

## Finding bugs

When it comes to finding bugs, I must admit that I’ve wasted quite some time
stumbling in the dark. I often knew that it*used* to work a couple of days ago
—but I had no idea*where exactly* things went wrong. It was only when I found
out about`git bisect` that I could speed up this process a bit. With the 
`bisect` command, Git provides a tool that helps you find the commit that
introduced a problem.

Imagine the following situation: we know that our current version (tagged “2.
0”) is broken. We also know that a couple of commits ago (our version “1.9”), 
everything was fine. The problem must have occurred somewhere in between.<
figure
>

![Illustration showing the commits between working and broken versions.][6]</
figure
>
This is already enough information to start our bug hunt with `git bisect`:

    $ git bisect start
    $ git bisect bad
    $ git bisect good v1.9
    

After starting the process, we told Git that our current commit contains the
bug and therefore is “bad.” We then also informed Git which previous commit is 
definitely working (as a parameter to`git bisect good`).

Git then restores our project in the *middle* between the known good and known
bad conditions:<figure>

![Illustration showing that the bisect begins between the versions.][7]</figure
>
We now test this version (for example, by running unit tests, building the app
, deploying it to a test system, etc.) to find out if this state works—or 
already contains the bug. As soon as we know, we tell Git again—either with
`git bisect bad` or `git bisect good`.

Let’s assume we said that this commit was still “bad.” This effectively
means that the bug must have been introduced even earlier—and Git will again 
narrow down the commits in question:<figure>


![Illustration showing how additional bisects will narrow the commits further.][8]
This way, you’ll find out very quickly where exactly the problem occurred.
Once you know this, you need to call`git bisect reset` to finish your bug hunt
and restore the project’s original state.

## A tool that can save your neck<aside class="sidebar">

## Also in Issue № 402

## [Running Code Reviews with Confidence][9]

by Emma Jane Hogbin Westby</aside>

I must confess that my first encounter with Git wasn’t love at first sight.
In the beginning, it felt just like my other experiences with version control: 
tedious and unhelpful. But with time, the practice became intuitive, and gained 
my trust and confidence.

After all, mistakes happen, no matter how much experience we have or how hard
we try to avoid them. What separates the pro from the beginner is preparation: 
having a system in place that you can trust in case of problems. It helps you 
stay on top of things, especially in complex projects. And, ultimately, it helps
you become a better professional.

## References

 [1]: img/branch-listing.jpg

 [2]: http://www.git-tower.com/learn/ebook/command-line/branching-merging/branching-can-change-your-life

 [3]: http://gitready.com/advanced/2009/01/12/fixing-broken-commit-messages.html
 [4]: img/reset-concept.jpg
 [5]: img/revert-concept.jpg
 [6]: img/bisect-01.jpg
 [7]: img/bisect-02.jpg
 [8]: img/bisect-03.jpg
 [9]: http://alistapart.com/article/running-code-reviews-with-confidence