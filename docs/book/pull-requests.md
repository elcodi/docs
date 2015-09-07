# Pull Requests

Elcodi is an Open Source project. This means that the community is a very
important part of the development (in fact, maybe the most important), and 
because of that each feature must be properly documented in order to make each
addition easy to understand and integrate.

You will find some information about how to create a pull request using github
in their 
[help article](https://help.github.com/articles/creating-a-pull-request/).

### Branch

The name of the branch should be explicative enough for everyone interested in
reviewing. Github is used to propose names like `patch-1`, so please, check the
name before any Pull Request.

You can use as well our format, that will help us to catalog more quickly your
work.

```
feature/this-is-my-new-feature
fix/fixes-this-thing
typo/fixes-this-typo
```

### Commits

Each branch can have as much commits as desired. But the question here is... how
many commits do I need in order to create an acceptable Pull Request? The answer
is as many as you need, but taking into account the possible usage of the git
rebase interactive tool.

Developers are used to creating partial commits named `WIP` and pushing them to
temporary branches. That's a good strategy if you want to ensure your daily 
work, but can be a bad idea if you use these commits for your Pull Request.

```
6d78a7b WIP
7843838 WIP
d78dbaa tmp
d7aa7df Some additions
```

As you can see here, these messages do not explain absolutely nothing about your
work, so please check your branches before any Pull Request.

### Commit description

How should a commit description describe our changes? Well, you can follow this
structure to follow good practices.

```
This is a short description

Closes #123

Then, We can add a larger description, focused on showing the world what is this
commit about. Don't hesitate to add as much information as needed.

* Finally, you can add some extra information using bullets
* And more bullets
* Related to this commit
```