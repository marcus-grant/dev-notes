Git Repositories with Submodules
================================

Add Submodule to a Git Repository
---------------------------------

- Use the `submodule` git commant to work with a remote repo that will associate with the parent repo
- `submodule add` will add a remote repo to the parent repo that will be tracked
- `submodule init` will modify the parent repo's configuration to recognize a submodule, once that submodule has been created or cloned within the parent.

```
# this registers the submodule to .gitmodule & clones its master branch
git submodule add -b master url.of.remote.repo path/to/repo/submodule
# this will initialize the parent repo to use the submodule
git submodule init
```
