# MkDocs github/ gh-pages deploy trick

#### **Disclaimer:**&#x20;

> `mkdocs build` doesn't allow the build of the `/site` within the same directory, hence the traditional gh-pages  deployment settings will fail and it won't deploy your site. (if its a org or user based pages)
>
> Hence the follwoing trick should work.&#x20;

If the output of your MkDocs build is in the `/site` folder, you need to push the contents of this folder to the `gh-pages` branch of your GitHub repository. However, you should not push the `/site` the directory itself but its contents. Here's how you can do it:

1. **Ensure Your Local Repository is Updated**:
   * Make sure your local repository is up to date with the main branch.
   * Build your MkDocs site using `mkdocs build`. This will update the `/site` directory.
2. **Move to the `/site` Directory**:
   * Navigate to the `/site` directory in your local repository.
3. **Create a Temporary Git Repository**:
   *   Since the `/site` the directory is not a Git repository, you need to initialize it temporarily:

       ```
       cd site
       git init
       ```
4. **Commit the Changes**:
   *   Add and commit the changes in the `/site` directory:

       ```
       git add .
       git commit -m "Deploy MkDocs Site"
       ```
5. **Push to `gh-pages` Branch**:
   *   Push the contents of the `/site` directory to the `gh-pages` branch of your remote repository. Replace `your-username` with your GitHub username.

       ```
       git push -f https://github.com/mumbaipy/website.git master:gh-pages
       ```
6. **Clean Up**:
   *   Once you've successfully pushed to `gh-pages`, you can delete the temporary Git repository in the `/site` directory:

       ```
       rm -rf .git
       ```
7. **Configure GitHub Pages**:
   * Go to your repository (`https://github.com/mumbaipy/website`).
   * Click on 'Settings' and scroll down to the 'GitHub Pages' section.
   * Set the source to the `gh-pages` branch.
8. **Access Your Site**:
   * Your MkDocs site should now be available at `https://mumbaipy.github.io/website/`.

This process ensures that the built HTML files in your `/site` directory are pushed to the `gh-pages` branch, allowing GitHub Pages to host your MkDocs site. Remember to repeat these steps every time you update your site.
