# Git & GitHub FAQ

These are common questions and issues on Git and GitHub.

> [!WARNING]
> This FAQ document is provided as a (hopefully!) helpful resource for students; however, it may contain errors, omissions, or outdated information. It is not a substitute for official course materials, lectures, or direct communication with instructors. Students are encouraged to verify important details through official sources and seek further assistance from course staff if needed. If you find any error (even a typo or broken link!) please let us know and we will fix it! 🙏

- [Git \& GitHub FAQ](#git--github-faq)
  - [Questions](#questions)
    - [Git, GitHub, what is that? Where can I learn it?](#git-github-what-is-that-where-can-i-learn-it)
    - [What is good quality of Git usage?](#what-is-good-quality-of-git-usage)
    - [How do I submit my project solution in my GIT repository?](#how-do-i-submit-my-project-solution-in-my-git-repository)
    - [How do I change the submission tag if I have already tagged one commit for submission?](#how-do-i-change-the-submission-tag-if-i-have-already-tagged-one-commit-for-submission)
    - [How do I update the tags in my local repo? I get rejection with "(would clobber existing tag)" message](#how-do-i-update-the-tags-in-my-local-repo-i-get-rejection-with-would-clobber-existing-tag-message)
    - [Is a tag the same as a release in GitHub?](#is-a-tag-the-same-as-a-release-in-github)
    - [Autograding in GitHub, really?](#autograding-in-github-really)
    - [Where are the repos for my course?](#where-are-the-repos-for-my-course)
  - [Problems and issues](#problems-and-issues)
    - [I cannot clone or push to GitHub with my password credentials](#i-cannot-clone-or-push-to-github-with-my-password-credentials)
    - [I get `Permission denied (publickey).` from GitHub](#i-get-permission-denied-publickey-from-github)
    - [I have committed to the remote repo but I am not listed as a "contributor", why?](#i-have-committed-to-the-remote-repo-but-i-am-not-listed-as-a-contributor-why)
    - [Commits not correctly associated to my GitHub account, why?](#commits-not-correctly-associated-to-my-github-account-why)
    - [We cannot create issues in our group repo from the project board!](#we-cannot-create-issues-in-our-group-repo-from-the-project-board)
      - [Associate your Project to an issue](#associate-your-project-to-an-issue)
      - [Create an issue from the Project Board](#create-an-issue-from-the-project-board)

## Questions

### Git, GitHub, what is that? Where can I learn it?

We will use proper (git) version control in all our programming projects. This is totally standard practice in the industry and you would have seen that in previous courses (SEF and SEPT at least). Said so, if you want to refresh or have a quick intro to it, here are two resources I found useful:

- [Learning Git and GitHub](https://www.linkedin.com/learning/learning-git-and-github-14213624/): An excellent course on on LinkedIn Learning Links introducing Git & Github.
- [Git Tutorial for Beginners: Command-Line Fundamentals](https://www.youtube.com/watch?v=HVsySz-h9r4).
- [30' video on Git & GitHub](https://youtu.be/jG4Vs81kMlc).
- [GitHub Guides](https://guides.github.com/).
- [Git \& GitHub Tutorial & Reference @ Javatpoint](https://www.javatpoint.com/git).

There are lots more good resources on the web of course!

### What is good quality of Git usage?

In assessments and exercise we expect students to apply quality Software Engineering processes, including usage of Git and GitHub. Please refer to the [SE and Git Practices FAQ](SE_GIT-PRACTICES.md).

### How do I submit my project solution in my GIT repository?

You submit by **tagging the _exact_ commit that you want to submit and be marked**, using the exact name given in the assignment specification. We will ONLY marked tagged submissions and will ONLY mark the tagged commit. Students can have many commits, and branches, even commits after the deadline. We will mark ONLY what you submit.

Of course we do not have access to your local machine, so the tag has to be created locally or then _pushed_ into GitHub remote repo (see below for several guides). Your tag has to show under `tags`, for example if the tag asked for is `submission`:

![google-form](imgs/tags-github-gui.png)

Observe that a tag `submission` is:

- NOT the same as a tag called "`Submission`" (i.e., tags are case-sensitive);
- NOT the same as a _branch_ called "`submission`";
- NOT the same as a _commit message_ "`submission`"; and
- NOT the same as a _release_ called "`submission`".

A tag is a specific point in the repository history, the point you want to be used for marking. A branch, a comment, and a release are different things.

While you can tag from your IDE (e.g., VS Code) you can always resort to command line to first create the tag in your local repo and then push it to the remote:

```shell
git tag -a submission <hash of commit to tag>
git push origin submission
```

Check the remote has the tag where you wanted!

Note that _a tag name can only be used once_, so if you already have a tag `submission` and want to use that tag name on another commit (e.g., you have a better, more recent, commit for your solution), you first need to delete the existing tag; see the next question for that. :-)

- For basic information on tagging, check [here](https://git-scm.com/book/en/v2/Git-Basics-Tagging).
- To create, push, and view tags in GitHub Desktop, check [here](https://docs.github.com/en/desktop/contributing-to-projects/managing-tags).
- To tag via command line or via GitHub web interface, check [here](https://stackoverflow.com/questions/18216991/create-a-tag-in-a-github-repository).

Note that the timestamp of the _commit_ is the submission date.

### How do I change the submission tag if I have already tagged one commit for submission?

This will happen when you realize you have a better version to submit than the one you submitted/tagged before. To do that, you need to first _delete_ the existing tag from both your local repo and from the server:

```shell
git tag --delete <tagname>  # first delete tag in the local repo
git push origin :refs/tags/<tagname>  # then delete remote tag
```

It is important to use `:refs/tags` when deleting the remote tag, as otherwise you may delete the branch with the same name! The empty string to the left of the colon causes the remote reference to be deleted!

Once the tag has been fully deleted, so you can re-use it on another commit!

See this as well on how to _rename_ an existing tag:

![google-form](img/how-to-rename-tag.png)

More information on how to delete git tags [here](https://devconnected.com/how-to-delete-local-and-remote-tags-on-git/).

### How do I update the tags in my local repo? I get rejection with "(would clobber existing tag)" message

To fetch the tags in the remote (e.g., tags pushed by other collaborators), you can do:

```shell
git fetch --all --tags -f
```

This will fetch all the changes from the remote, but also all the tags. The `-f` option will replace an existing tag (e.g., `submission`) with the one in the remote, if any (instead of failing). This could come very handy when different collaborators tag and re-tag commits as they incrementally work on a solution.

### Is a tag the same as a release in GitHub?

No, a _tag_ is a _git concept_, whereas a `Release` is something about GitHub, beyond git itself. So, they are not synonymous.

A tag is a _pointer_ to a specific commit, that's all, you basically give a name to a specific commit. This is what we use to mark the commit that is meant to be submitted for marking.

### Autograding in GitHub, really?

When you push to your `main` branch, GitHub will run the tests and award the marks for the various exercises. You can see the result of the last commit (whether it fully passed or failed some test):

![autograding in GH](imgs/gh_autograding_00.png)

If you click on the mark (tick or cross) or go to Actions, you can see the autograding run:

![autograding in GH](imgs/gh_autograding_01.png)

If you open the `education/autograding@v1` section, a perfect solution will look as follows:

![autograding in GH](imgs/gh_autograding_02.png)

Isn't this cool? :heart_eyes:

### Where are the repos for my course?

During the course you will fork many GitHub repos, for workshop exercises and assessments. You can access your course repos by going to the course organisation URL:

![](imgs/gh_course_student.png)

Note you also have access to any public repo, like the FAQ repo.

## Problems and issues

### I cannot clone or push to GitHub with my password credentials

As [per August 12th, 2021 GitHub post](https://github.blog/changelog/2021-08-12-git-password-authentication-is-shutting-down/), GitHub is not no longer accepting account passwords when authenticating Git operations, like cloning private repos or pushing changes. You should use **token-based authentication**, such as  personal access, OAuth, SSH Key, or GitHub App installation token.

So, if you were still using a password to authenticate your GitHub.com operations (something never recommended anyways if you are doing development), you must start using a [personal access token](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token) by August 13, 2021 via HTTPS (recommended) or [SSH key](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh) to start using a personal access token to avoid disruption.

For example, you can set-up your remote as follows (after you generated your token in GitHub):

```shell
git remote set-url origin https://<token>@github.com/<username>/<repo>
```

As explained [here](https://www.sobyte.net/post/2021-08/github-deprecates-passwords-for-git-operations/), tokens offer many advantages over password-based authentication:

- **Unique:** tokens are specific to GitHub and can be generated on a per-use or per-device basis.
- **Revocable:** tokens can be individually revoked at any time without the need to update unaffected credentials.
- **Limited:** tokens can be narrowed to allow only the access required by the use case.
- **Random:** tokens are not subject to dictionary types or brute force attempts that might be made with simpler passwords that users need to remember or enter periodically.

### I get `Permission denied (publickey).` from GitHub

If yo get this error

```shell
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.
```

it most probably mean you have not correctly set-up your ssh keys into GitHub. GitHub needs your ssh public key to know it's you who is trying to access the repo. Please check [here](https://docs.github.com/en/authentication/troubleshooting-ssh/error-permission-denied-publickey).

Setting up GitHub to access with token or ssh is fundamental to have a productive environment, as you will be pushing and pulling from GitHub a lot! :-)

### I have committed to the remote repo but I am not listed as a "contributor", why?

The two main reasons may be:

1. Your commit is in a branch and has not yet made it to the default (master/main) branch, therefore you did not technically contribute (yet).
2. Your local Git commit email isn't connected to your account; [connect it](https://docs.github.com/en/github/setting-up-and-managing-your-github-user-account/managing-email-preferences/setting-your-commit-email-address)!

Read [this GitHub page](https://docs.github.com/en/github/setting-up-and-managing-your-github-profile/managing-contribution-graphs-on-your-profile/why-are-my-contributions-not-showing-up-on-my-profile#common-reasons-that-contributions-are-not-counted) to understand more about why your commit is not yet counting as contributions.

### Commits not correctly associated to my GitHub account, why?

Please check [doc in GitHub](https://docs.github.com/en/github/committing-changes-to-your-project/troubleshooting-commits/why-are-my-commits-linked-to-the-wrong-user) and also [this post](https://crunchify.com/how-to-set-github-user-name-and-user-email-per-repository-different-config-for-different-repository/) if you want to set-up different GitHub accounts for different users.

Please fix it so we can know the commit was _yous_. Otherwise we may get your contributions wrong and risk getting lower marks or delaying your marking.

### We cannot create issues in our group repo from the project board!

When working in teams, [GitHub Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects) are an excellent collaboration tool, just fantastic. 👏

The idea is that every (or most) entries in your project board, would probably be issues in your repo.

The first thing you need to do is to make sure the **GitHub Project is linked in the repo**. This will have many benefits, including automatically update of an entry's status in the Project view when the issue's state changes (e.g., from “In progress” to “Done” when issue is closed). This can be done in the `Project` tab of your repo, either by creating the Project from there (best option!) or linking the project. This is how it looks once linked:

![](imgs/gh_project_link_repo.png)

OK, once the project is linked you would be able to:

1. Create an issue in the Project Board (e.g., a new "Backlog" entry that is a newissue)
2. Associate your Project to an issue.

#### Associate your Project to an issue

Let's stat with the second one. On the right hand side of an issue you can pick your project, so that the issue is assigned to the project:

![](imgs/gh_project_issue_link.png)

Once the issue is there, you can select and update it's project statuses:

![](imgs/gh_project_link_repo_options.png)

#### Create an issue from the Project Board

Another way to use the project is to add an entry to your project board (e.g., a new "TODO" or "Backlog") and right there make it an issue in your repo.

When you type the description, you will be given the option to either create a new issue or re-use one that exists:

![](imgs/gh_project_create_issue_01.png)

>[!WARNING]
>If you cannot see it the first time you work on the project, type part of your repo name until GH finds it. If still your repo doesn't show up, do at least one issue an link it to the project as explained above. Somehow, GH takes a while to list your repo. But if you search for it typing a few letters, it should come up. 😉 **❗Be very careful not creating the issue in another repo of yours, or in a public repo!** If you do that by mistake, **transfer** it to your project repo (the issue page has a transfer option on the bottom right).
