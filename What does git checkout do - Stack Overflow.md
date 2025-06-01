The `git checkout` command is _very complicated_. It's so complicated that someone finally, as of Git 2.23, split it into two separate commands:

-   `git switch`, which does the "select other branch or commit to have checked out" operation; and
-   `git restore`, which does the "update some files in index and/or working tree" operation.

This still doesn't mention several additional modes of operation (`git checkout -m` for instance),<sup>1</sup> but at least separates out all the "restore files" options, of which there are many.

You're using the "restore files" mode of `git checkout` and, as [shrey deshwal noted](https://stackoverflow.com/a/69827282/1256452), this operation will:

-   write to your working tree (always); and
-   write to your index / staging-area (sometimes).

When using `git restore` instead of `git checkout`, _you_ control which of the index/staging-area and working-tree files are updated, using the `-S` (staging area) and `-W` (working tree) options. This is not possible with `git checkout`: `git checkout` always writes to the working tree, and also writes to the index/staging-area _if_ you specify a commit or tree object as the source of the file to be written to the working tree.

If you have Git 2.23 or later, use `git restore` to do this: its operation is less confusing and more direct. You specify the `--source` for the file, or let it default to using the index:

```
git restore --source 11cb5b6 -- hello.txt
```

This writes to the working tree (only). Or, add `-S` and/or `-W` to write to the staging area (index) with `-S`, and/or working tree with `-W`:

```
git restore --source 11cb5b6 -SW -- hello.txt
```

This writes to both staging-area and working-tree (because both `-S` and `-W` are given).

By contrast, `git checkout -- file` makes the source less obvious (it's the same as it is in `git restore`, but less obvious) and gives you no choice of target(s) (always `-W`, but `-S` gets added if the source is a commit or tree). The `git restore` command also documents the `--overlay` vs `--no-overlay` modes properly. This option applies only to the "restore files" mode of `git checkout` (where it is now documented, but it's not clear that it only applies to this mode!).

___

<sup>1</sup>The `-m` option to `git checkout`:

-   re-creates a merge conflict, or
-   does a merge operation during a checkout, as if you ran a rather complicated series of Git commands that ends up with you on the target branch, having then merged against the uncommitted code in your working tree.

This second operation is somewhat dangerous: as the documentation now notes,

> When switching branches with `--merge`, staged changes may be lost.

The first operation cheerfully destroys any merge work you started in the working tree copy of the file. So `git checkout -m` is always "dangerous" in the `git restore` way: it will wipe out uncommitted work without asking. I kind of wish that these had not been left in the `git switch` command, but they were.

___

# Read only if the above still doesn't make sense:

### If this stuff still doesn't come together, you're probably missing out on a key concept in Git: how the index / staging-area really works.

A Git _repository_ is, to a large extent, just a big database of _commits_. What you do with this repository is _add more commits_. Each commit itself is:

-   Numbered. Each commit has a big, ugly, incomprehensible _hash ID_ number such as <sup><sub><code>e9e5ba39a78c8f5057262d49e261b42a8660d5b9</code></sub></sup> (often abbreviated, e.g., `e9e5ba3`). These _appear_ random, though in fact they're entirely non-random.
    
-   Storage: each commit stores two things:
    
    -   A commit has a _full snapshot of every file_. Commits don't store _changes_, so when Git shows you changes, it's really doing a `git diff` of two _snapshots_.
        
    -   A commit also stores some _metadata_, or information about the commit itself. This includes things like the name and email address of the author of the commit (from `user.name` and `user.email`). It includes some date-and-time stamps. It includes a log message, which `git log` or `git show` will show before any diffs. And, crucially for Git's internal operation—though we won't cover any of the details here—each commit stores a _list of previous commit hash IDs_. Most commits just store one such hash ID, which is the "parent" of the commit. That's how Git finds the previous commit, so that it can show you what _changed_.
        

All of this stuff inside the commit is completely, totally, 100% read-only: no part of any commit can ever be changed once it's made.<sup>2</sup> But this leaves us with a dilemma: if no part of any commit can ever _change_, how can we get any _new work_ done?

Git's answer is the same as the answer is other version control systems: sure, the _commits_ are read-only forever, but you don't _do work_ with the _committed_ files. We _copy the files out of the commit_, into a work area. You work on / with those files. That area is your _working tree_ or _work-tree_. The copied-out files are ordinary read/write files, that your computer can do ordinary work on or with.

So far, none of this is particularly bizarre or incomprehensible. A commit is like an archive of files, like a `tar` or `rar` file made out of other files, but with special Gitty features like metadata and a weird random-looking number. We use `git checkout` or `git switch` to pick one: Git extracts the files, and now we can work on them.

But here's where Git gets weird. If you've used _other_ version control systems, you are probably used to this idea: you work on the files and then you tell the VCS to commit them and it does. That would be simple, so Git doesn't do that.

When Git goes to build a _new_ commit, Git **does not use your files** at all! Instead, Git uses a _secret extra copy_. Only it's not actually _secret_, and it's not usually a copy either. What it is, is _hidden_. This extra "copy" of each file is in what Git calls by three different names:

-   The _index:_ a meaningless term. Meaningless is sometimes good, because then there's no preconceived notion to push out of the way for some weird technical reason. But it makes it a bit hard to remember.
    
-   The _staging area:_ this is how you _use_ the index, so it makes sense. But this obscures the technical details, which do matter. You need to be aware of them.
    
-   The _cache:_ this is the worst name of all, because it's how Git itself sometimes uses the index, but not how _you_ use it, and doesn't cover all the ways that Git uses the index. This term is mostly defunct, except that it appears in flags like `git rm --cached` or `git diff --cached`.
    

Sometimes the `--cached` flag has `--staged` as a synonym: `git diff --staged` does exactly the same thing as `git diff --cached`. Sometimes they don't: `git rm --staged` rejects the `--staged` entirely. Oddly, `git restore` has _only_ `--staged`. Getting rid of `--cached` entirely might be a good direction; maybe Git will eventually do that. But in any case, you need to know all three names, as "the index" appears in various places. In particular, the index has a special role during conflicted merges, and it determines whether files are _tracked_ or _untracked_. We won't go into this level of detail here; we'll only talk about the index as it pertains to making new commits.

When you run `git commit`, instead of Git reading from your working tree to find out which files you've changed, Git simply _packages up the files that are in its index at this time._<sup>3</sup> **To make _that_ work, the initial `git checkout` or `git switch` step first fills in Git's index.**

What this means is that after you've checked out some commit to work on it, you have _three_ "copies" of each file:

-   There is a read-only, Git-ified, compressed-and-de-duplicated special format version of the file in the commit.
-   There is another "copy" (see below for the reason for the quotes here) of that file in Git's index / staging-area.
-   Last, there's a _usable_ copy (no quotes this time) of the file in your working tree. That's the one you can see and edit.

When Git _stores_ a file permanently in a commit, it:

-   compresses the file, so that it takes less space;
-   sometimes, _super_\-compresses the file (this happens late in the game);
-   always Git-ifies the file: it's not stored _as_ a _file_ (with a name and other OS attributes), but rather as an internal Git _blob object_ (nothing but a hash ID name, no modes, etc.; the names and modes are stored in additional Git objects called _tree objects_).

In other words, these files are Git-ified and put into a database, rather than kept as regular files. Whenever Git does this, it _automatically de-duplicates the content_. So even though every commit stores _every_ file, the _repository_ doesn't bloat up out of control when you have millions of commits, as many of the commits have the _same_ copy of some file, and those are all shared. Instead of a _copy_ of the file, we get a "copy" of the file, inside the commit: hence the quotes.

The index stores pre-Git-ified, pre-compressed and pre-de-duplicated "copies" in this same way that commits do. The `git add` command therefore:

1.  reads the _working tree_ version of the file;
2.  compresses it and otherwise Git-ifies it: this produces an internal hash ID for de-duplication purposes;
3.  decides whether the file is a duplicate, or not.

If the file _is_ a duplicate, `git add` throws out the copy it just made: we don't need that one, we have one in the repository already. Git updates its index with the duplicate, and the file is now ready to be committed, all stored in Git's index.

If the file _isn't_ a duplicate, `git add` takes the now-prepared compressed file and readies it to go into the database, sort of temporarily added.<sup>4</sup> And now Git has a copy that is ready to be committed, stored in its index.

So:

-   we _started_ with some file `path/to/file.ext` in the index, ready to be committed;
-   then we Git-ified the (real, OS-level) file `file.ext` in folder `to` in folder `path` with `git add`;
-   then `git add` updated the index "copy" if needed, and now we have `path/to/file` in the index, ready to be committed.

Hence, _before_ `git add`, the index contained the _proposed next commit_. _After_ `git add`, the index still contains the _proposed next commit_. What `git add` just did was _update the proposed next commit_.

If you add an _all new_ file, the same sequence happens: `git add` reads and compresses the ordinary OS-level file, figures out whether the contents are a duplicate or not—maybe you've added `world.txt` which also contains `hello world`, which is a duplicate of existing `hello.txt` for instance—and `git add` has updated the proposed next commit so that new file `world.txt` is listed there too.

In all cases, Git always has the _proposed next commit_ set up in its index.<sup>5</sup> Running `git commit` means _use the proposed next commit as it is now_, which is why you can partially stage stuff: what you're doing is adding, to the index, copies of _some_ files that _do_ match the working tree copies, and copies of _other_ files that _don't_ match the working tree copies. Since the index holds _its own_ copies (or "copies" due to pre-de-duplication), this means there are always _three_ "active copies" of each file:

-   There is the `HEAD` (or current commit) copy. This one is Git-ified and _can't be changed_ because it is in a commit.
    
-   There is the index copy. This one is Git-ified, but _can_ be changed, because it's only a _proposed_ commit, not a real one yet.
    
-   Finally, there's the working tree copy. This is the only one you can see and work on / with.
    

You use and modify the working tree copies. You use `git add` or `git rm` to create or remove the _index_ copies, and then you use `git commit` to turn the _proposed_ next commit into an actual commit.

___

<sup>2</sup>This means that `git commit --amend` is a lie. It doesn't _amend_ the commit, it makes a _new and improved replacement_. The old commit still exists! This is true of `git rebase` as well. Things that, in Git, seem to change commits, don't really change them at all. You can tell by saving and comparing those hash IDs—but humans normally just sort of bleep right over them, which is a good thing: it lets us replace bad commits with better ones, without humans noticing that we did that.

<sup>3</sup>The `git commit` command offers a flag, `-a`, that means in effect: _first, run `git add -u`, then run `git commit`_. There are a bunch of subtleties about this, but the key item to note here is that this runs `git add -u`. The `-u` option will only update _tracked_ files, so you can't use this for _new_ files. Git therefore forces you to learn about `git add`.

<sup>4</sup>Git in fact just adds the object right away, and throws it away later if appropriate, but you can view this as "temporarily added" if you prefer.

<sup>5</sup>When you're in the middle of a conflicted merge, Git _knows_ that you're in the middle of a conflicted merge because there are some index entries that have a higher "staging number" than staging-number-zero. In this mode, Git won't make a new commit from the index at all, so one can argue that in this mode, the index _doesn't_ hold the proposed next commit.