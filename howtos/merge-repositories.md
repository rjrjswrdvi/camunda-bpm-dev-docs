# Merging Repositories

Some helpful `git` commands to help you with merging a whole repository, or just a subfolder, into 
another repository. You can execute the first part of the commands as a `bash` script and do the 
actual merge manually since a commit message will need to be provided.

Git recommends not to use `git-filter-branch` anymore, as it is error-prone and full of 'gotchas
generating mangled history rewrites'. It recommends to use an alternative filtering tool such as
[git filter-repo](https://github.com/newren/git-filter-repo/) instead. The `git` commands below use
the `git filter-repo` command.

## Merge a subdirectory of repository A into a subdirectory of repository B

```bash
#!/bin/bash

# GitHub username
username='camunda'

# repo to merge from
repoA='camunda-commons'

# repo to merge in
repoB='camunda-bpm-platform'

# the name of the repo A directory that we want to merge in repo B 
dirName='typed-values'

# new directory name of repo A in repo B directory structure
# it will additionally be used as a prefix for the repo A tags that will get merged
newDirName='engine-dmn'

# merge branch for repo A
mergeBranch='to-be-merged'

# remote for repo A in repo B
remoteName=$repoA

# clone both repositories
git clone https://github.com/$username/$repoA
git clone https://github.com/$username/$repoB

# go to the repository that contains the folder to be merged
cd $repoA

# create a merge branch
git checkout -b $mergeBranch

# Filter out and keep all the commits related to $dirName 
git filter-repo --path $dirName --to-subdirectory-filter $newDirName --tag-rename '':'$newDirName-'

# move to the repository to merge to
cd ../$repoB

# add a remote to the local path of repo A (the original directory, not the sub-folder)
git remote add $remoteName ../$repoA
```

Do the following manually, since a commit message will need to be added
```bash
# Merge repo A with repo B. This will result with a merge commit. 
# Give the merge commit a meaningful message (like chore(engine): merge dmn-engine repo into platform)
git pull --allow-unrelated-histories $remoteName $mergeBranch

# You can check the result in a nice way like this
git log --oneline --graph
```

## Merge a whole repository A in a subdirectory of repository B

```bash
#!/bin/bash

# GitHub username
username='camunda'

# repo to be merged
repoA='camunda-engine-dmn'

# repo to merge in
repoB='camunda-bpm-platform'

# new directory name of repo A in repo B directory structure
# it will additionally be used as a prefix for the repo A tags that will get merged
newDirName='engine-dmn'

# merge branch for repo A
mergeBranch='to-be-merged'

# remote for repo A in repo B
remoteName=$repoA

#clone both repositories
git clone https://github.com/$username/$repoA
git clone https://github.com/$username/$repoB

# go to the repo to be merged
cd $repoA

# create a merge branch
git checkout -b $mergeBranch

# Rewrite commits of repo A such that all changes are applied in the desired subfolder.
# All the toplevel files/directories need to be listed with a separate --path argument
git filter-repo [--path FILE_OR_DIR_NAME] --to-subdirectory-filter $newDirName --tag-rename '':'$newDirName-'

# move to repo B
cd ../$repoB

# add a remote to the local path of repo A (the original directory, not the sub-folder)
git remote add $remoteName ../$repoA
```

Do the following manually, since a commit message will need to be added
```bash
# Merge repo A with repo B. This will result with a merge commit. 
# Give the merge commit a meaningful message (like chore(engine): merge dmn-engine repo into platform)
git pull --allow-unrelated-histories $remoteName $mergeBranch

# You can check the result in a nice way like this
git log --oneline --graph
```

## Merge the Maven modules (incomplete)

When merging Java projects, make sure that the Maven modules are covered by the release builds, so that artifacts will become available on Maven central. E.g. for `camunda-bpm-platform`, make sure the new modules are covered by the [sonatype-oss-release profile](https://github.com/camunda/camunda-bpm-platform/blob/7.11.0/pom.xml#L187-L205).

## Resources

- [GitHub](https://help.github.com/en/articles/splitting-a-subfolder-out-into-a-new-repository)
- [Zeebe](https://github.com/zeebe-io/zeebe/wiki/Merge-repositories)
- [mattsch](https://mattsch.com/2015/06/19/move-directory-from-one-repository-to-another-preserving-history/)
