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
However, no sign-off shall be added by the person cherry picking the commit and the original commit message shall be left unmodified, only the commit title edited to preprend `[zep fromtree]` to it.

### `fromlist`

This is equivalent to `fromtree`, except that `fromlist` is for commits that have been submitted but not yet merged upstream.

We have an upstream-first approach, so any changes that can be upstreamed should first be submitted upstream.
A `fromlist` comes in handy for cherry-picking changes that are still in the review process.
They should however have reasonable expectations to be merged with few modifications upstream, and it is preferable to get at least a first round of reviews on the changes upstream before cherry-picking them to ensure that.

A `fromlist`, just as a `fromtree`, shall have a no added sign-off.
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
As you are modifying the original commit, add your own sign-off at the bottom of the commit message.
Also, whenever relevant make sure to document the rationale for what you did in the commit message between the sign-off of the upstream author and your own sign-off.

The tags of such commits are to be appended with `dirty`. For example, the tag of such a `fromtree` commit would be: `[zep fromtree dirty]`

### `noup`

This is for local patches that are not expected to make their way upstream.
<br>
This should be used sparingly and for good reasons, as we do not want to deviate from upstream. This is not meant as a quick way to get changes into this repository by bypassing upstream and such commits will be rejected.

`noup` commits are like regular commits in the main Zephyr repository with the exception that their commit title is prepended with `[zep noup]`.

Version bumping
---------------

This section talks about Mbed TLS, but the same guidelines apply to the other
forks of the TF-M and Mbed TLS projects.

Let's say that a new Mbed TLS release is announced and that its version is `3.6.5`.
On the Zephyr fork:
1. Create a new branch named `zephyr_<project_name>_v<upstream_release_version>`
   which is located exactly at the upstream release tag. For example,
   `zephyr_mbedtls_v3.6.5`.
2. Make a PR by cherry-picking all the Zephyr extra patches that are still needed
   for the new branch.
   - Discard all the `[zep fromtree]` and `[zep fromlist]` commits that are already
      included in the release. As for `[zep noup]` they might no more be valid
      or might require an update so they should be checked individually.
   - The cherry-picking order should be: `[zep fromtree]` first, then
       `[zep fromlist]` and finally `[zep noup]`.
   - Cherry-pick commits without `-x` nor `-s` so as not to inflate the commit
     message over time, unless you had to modify the commit and it's not just a
     clean cherry-pick.
3. When creating the PR set the base branch to the newly-created one. In our example,
   it would be `zephyr_mbedtls_v3.6.5`.
   - With this approach the PR in GitHub will only show Zephyr's extra commits
     on top of the upstream release tag, so it will be easier to review.
4. Get the PR in Mbed TLS fork approved and merged.
5. Set the new branch (in our example, `zephyr_mbedtls_v3.6.5`) as the default
   branch of the repository.
   - When this is done, tag the HEAD of the previous default branch (which in our
     example would be `zephyr_mbedtls_v3.6.4`) with the name same as the branch
     (in order to keep its history) and remove that branch.
