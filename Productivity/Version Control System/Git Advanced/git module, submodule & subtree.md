If you have cloned your friend's GitHub repository inside your own GitHub repository, you can use them as one repository by merging the two repositories together. Here are the general steps to merge the repositories:

1. Create a new branch in your GitHub repository.

2. In your local terminal, navigate to your friend's GitHub repository.

3. Use the git remote command to add your GitHub repository as a remote repository. For example, you can use the following command:

   ```
   csharpCopy code
   git remote add my-repo <URL of your repository>
   ```

4. Fetch the changes from your GitHub repository using the following command:

   ```
   perlCopy code
   git fetch my-repo
   ```

5. Checkout the branch you created in step 1.

6. Merge the changes from your friend's GitHub repository into your branch using the following command:

   ```
   bashCopy code
   git merge my-repo/master
   ```

   This will merge your friend's repository into your branch.

7. Resolve any merge conflicts that occur during the merge process.

8. Commit the changes and push the merged branch to your GitHub repository.

Once you have completed these steps, your friend's repository will be merged into your repository and you can use them as one repository.

<br>

If you need to continuously clone your friend's repository, you can consider adding your friend's repository as a submodule in your own repository. This will allow you to keep your friend's repository separate from your own, while still being able to easily access and use its contents.

Here are the steps to add a submodule to your repository:

1. In your local terminal, navigate to the root directory of your own repository.

2. Use the following command to add your friend's repository as a submodule:

   ```
   vbnetCopy code
   git submodule add <URL of your friend's repository>
   ```

3. This will create a new directory in your repository's root directory containing your friend's repository files. You can now use these files just as you would any other files in your repository.

Whenever you need to update your friend's repository, you can simply navigate to the submodule directory and run `git pull` to pull in the latest changes. This will ensure that your friend's repository is always up-to-date in your own repository.

<br>

Yes, you can use Git subtree instead of Git submodule to include your friend's repository inside your own repository. Git subtree allows you to merge two repositories together, while keeping their commit history separate.

Here are the steps to use Git subtree:

1. In your local terminal, navigate to the root directory of your own repository.

2. Use the following command to add your friend's repository as a remote:

   ```
   vbnetCopy code
   git remote add -f friend-repo <URL of your friend's repository>
   ```

3. Use the following command to merge your friend's repository into a new directory within your own repository:

   ```
   arduinoCopy code
   git subtree add --prefix=friend-dir friend-repo master
   ```

   This will create a new directory called `friend-dir` in your repository's root directory, containing your friend's repository files.

4. Whenever you need to update your friend's repository, you can run the following command:

   ```
   arduinoCopy code
   git subtree pull --prefix=friend-dir friend-repo master
   ```

   This will pull in the latest changes from your friend's repository and merge them into the `friend-dir` directory in your repository.

Using Git subtree allows you to easily incorporate your friend's repository into your own repository, while still keeping their commit history separate. However, it's worth noting that Git subtree can be a bit more complex to use than Git submodule, so it may take some time to get used to the workflow.

<br>

***

### 참고

- [레파지토리의 하위 디렉토리를 다른 레파지토리로 분리하기](https://ashortday.tistory.com/58)
- [Git의 서브모듈](https://sgc109.github.io/2020/07/16/git-submodule/)
- [Github Repository 합치기(feat. subtree)](https://yeonyeon.tistory.com/169)
- [Git subtree 사용법](https://www.three-snakes.com/git/git-subtree)
- [Git 서브모듈 사용법](https://data-engineer-tech.tistory.com/20)

