This project is the target being used to test the instructions at https://medium.com/@ayushya/move-directory-from-one-repository-to-another-preserving-git-history-d210fa049d4b for copying a directory from one git repository to another while preserving the history.

# Getting files ready to move from SourceRepository.
**Step 1**: Make a copy of SourceRepository as the following steps make major changes to this copy which you should not push!
```
mkdir Source
cd Source
git clone --branch <branch> --origin origin --progress \
  -v <git SourceRepository url>
# eg. git clone --branch develop --origin origin --progress \
#   -v https://github.com/username/SourceRepository.git
# (assuming SourceRepository is the repository you want to copy from)
```
**Step 2**: Go to that directory.
```
cd <git SourceRepository directory>
#  eg. cd SourceRepository
# Folder Path is ~/Source/SourceRepository
```
**Step 3**: To avoid accidentally making any remote changes (eg. by pushing), delete the link to the original repository.
```
git remote rm origin
```
**Step 4**:
<!-- Go through your history and files, removing anything that is not in FOLDER_TO_KEEP. The result is the contents of FOLDER_TO_KEEP spewed out into the base of SourceRepository.
```
git filter-branch --subdirectory-filter <directory> -- --all
# eg. git filter-branch --subdirectory-filter subfolder1/subfolder2/FOLDER_TO_KEEP -- --all
``` -->
## Select Files to Keep
Use git filter-repo to remove unwanted content
```
# Keep only the ingestion folder
git filter-repo --path ingestion
# Use the invert paths flag to keep everything except the provided paths
git filter-repo --path ingestion/config/exclude0 --path ingestion/config/exclude1 --path ingestion/src/exclude0 --path ingestion/src/exclude1 --invert-paths
```


**Step 5**: Clean the unwanted data. **may be partially unnecessary, some steps included in git filter-repo command**
```
git reset --hard
git gc --aggressive
git prune
git clean -fd
```
**Step 6**: Move all the files and directories to a NEW_FOLDER which you want to push to TargetRepository.
```
mkdir <base directory>
#eg mkdir NEW_FOLDER
mv * <base directory>
#eg mv * NEW_FOLDER
```
Alternatively, you can drag all the files and directory to the NEW_FOLDER using GUI.
**Step 7**: Add the changes and commit them.
```
git add .
git commit
```

# Merge the files into the new TargetRepository.
**Step 1**: Make a copy of TargetRepository if you don’t have one already.
```
mkdir Target
cd Target
git clone <git TargetRepository url>
# eg. git clone
https://github.com/username/TargetRepository.git
```
**Step 2**: Go to that directory.
```
cd <git TargetRepository directory>
#  eg. cd TargetRepository
# Folder Path is ~/Target/TargetRepository
```
**Step 3**: Create a remote connection to SourceRepository as a branch in TargetRepository.
```
git remote add source-repo <git SourceRepository directory>
# (source-repo can be anything - it's just a random name)

# eg. git remote add source-repo ~/Source/SourceRepository
```
**Step 4**: Pull files and history from the source branch (containing only the directory you want to move) into TargetRepository.
```
git pull source-repo develop --allow-unrelated-histories
# This merges develop from SourceRepository into TargetRepository
```
**Step 5**: Remove the remote connection to SourceRepository.
```
git remote rm source-repo
```
**Step 6**: Finally, push the changes
```
git push
```
You can delete both the cloned repositories.
The files changes with history are now available online in TargetRepository.