This repository is Zephyr's fork of [Mbed TLS](https://github.com/Mbed-TLS/mbedtls).

Additional patches
------------------

On top of upstream releases, additional patches (commits) are applied to this repository as necessary.
<br>
They are marked with different tags depending on their type.
<br>
For every one of them, their commit title is preprended with `[zep <patch type>]`.
<br>
Below is a description of the various patch types.

### `fromtree`

This is the most common and preferred patch type.
<br>
A `fromtree` is simply a direct, clean cherry-pick of an upstream commit.

It shall be cherry-picked with the `-x` flag so that the hash of the upstream commit is present in the commit message for tracking purposes.
A sign-off shall also be added by the person cherry-picking the commit.
The original commit message shall be left unmodified; only the commit title should edited to prepend `[zep fromtree]` to it.

### `fromlist`

This is equivalent to `fromtree`, except that `fromlist` is for commits that have been submitted but not yet merged upstream.

We have an upstream-first approach, so any changes that can be upstreamed should first be submitted upstream.
A `fromlist` comes in handy for cherry-picking changes that are still in the review process.
They should however have reasonable expectations to be merged with few modifications upstream, and it is preferable to get at least a first round of reviews on the changes upstream before cherry-picking them to ensure that.

A `fromlist`, just as a `fromtree`, shall have a sign-off added.
In contrast, however, it must not be cherry-picked with the `-x` flag. This is because in most cases the hash won't point to the upstream commit once it's merged upstream.
Instead, the following shall be done, depending on the upstream review system:
- For a GitHub-based project, the following line shall be added at the bottom of the commit message (after the sign-off of the upstream author but before your own): `Upstream PR #: <PR number>`
  <br>
  Do not put a direct link to a GitHub PR because GitHub will clutter those PRs by indicating that they were referenced in a commit.
- For a Gerrit-based project, nothing shall be added. The commit message already contains a `Change-Id` line which allows to find the change upstream.

`fromlist` commits do not need to be reverted and replaced by `fromtree` ones when they are merged upstream. This will be taken care of by the next person who updates this repository to a new upstream revision.

Side note: `fromlist` is called that way due to historical reasons and the fact that Linux uses mailing lists for code review.

### Altered `fromtree`/`fromlist` cherry-picks

For both `fromtree` and `fromlist` commits, sometimes a direct, unmodified cherry-pick from upstream is not an option because:
- It causes conflicts that cannot be easily solved by cherry-picking additional upstream commits;
- Or unacceptable changes would be introduced as part of the cherry-pick(s).

In those cases it is acceptable to resolve the conflicts manually and/or remove the undesired changes.
However make sure to document what you did and the rationale at the very bottom of the commit message, right before your own sign-off.

The tags of such commits are to be appended with `dirty`. For example, the tag of such a `fromtree` commit would be: `[zep fromtree dirty]`

### `noup`

This is for local patches that are not expected to make their way upstream.
<br>
This should be used sparingly and for good reasons, as we do not want to deviate from upstream. This is not meant as a quick way to get changes into this repository by bypassing upstream and such commits will be rejected.

`noup` commits are like regular commits in the main Zephyr repository with the exception that their commit title is prepended with `[zep noup]`.
