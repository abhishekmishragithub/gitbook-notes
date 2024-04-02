# 🐛 Signing multiple git commits



To sign all commits, you need to set up git to automatically sign all commits by default. Here's how you can do it:

1. First, check if you have a GPG key:

```bash
gpg --list-secret-keys --keyid-format LONG
```

2. If you don't have a GPG key, you need to generate one:

```bash
gpg --full-generate-key
```

3. Once you have a GPG key, configure Git to use it:

```bash
git config --global user.signingkey YOUR_GPG_KEY_ID
```

Replace `YOUR_GPG_KEY_ID` with your GPG key ID.

4. Set Git to sign all commits by default:

```bash
git config --global commit.gpgsign true
```

5. Now you need to rebase your branch and sign all commits:

```bash
git rebase -i origin/main
```

In the text editor that opens, replace `pick` with `edit` for each commit you want to sign, then save and close the editor.

6. Sign the commit:

```bash
git commit --amend --no-edit -S
```

7. Continue the rebase:

```bash
git rebase --continue
```

8. Repeat steps 6 and 7 for each commit.
9. Once you've signed all commits and the rebase process is complete, you can force push your branch to the remote repository with:

```bash
git push origin docs/ADP-2027/improve-issue-controller-swagger --force
```

