# Style Guide


## Philosophy

TODO

## Best practise 

### Programming Language

#### Lint

make sure you run `pylint` on your code before commit
[Google style guide - Pyguide lint](https://google.github.io/styleguide/pyguide.html#21-lint)

If you are using PyCharm IDE, you can also install this plugin [pylint-pycharm](https://github.com/leinardi/pylint-pycharm) 

#### naming

Following the [Google style guide (Python) - file naming](http://google.github.io/styleguide/pyguide.html#3163-file-naming)

#### docstring

Following the [Google style guide (Python) - Comments and Docstrings](https://github.com/google/styleguide/blob/gh-pages/pyguide.md#38-comments-and-docstrings)
if you are using PyCharm, you can setting the docstring to Google style by following steps:  

> preferences -> tools -> Python integrated tools  
> Change the Docstring format from default reStructuredText to Google 

### Common Engineering rule

#### commit message format

```text
<type> (feat fix docs style refactor test chore): Summarize changes in around 50 characters or less

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
subject of the commit and the rest of the text as the body. The
blank line separating the summary from the body is critical (unless
you omit the body entirely); various tools like `log`, `shortlog`
and `rebase` can get confused if you run the two together.

Explain the problem that this commit is solving. Focus on why you
are making this change as opposed to how (the code explains that).
Are there side effects or other unintuitive consequenses of this
change? Here's the place to explain them.

Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet, preceded
   by a single space, with blank lines in between, but conventions
   vary here

If you use an issue tracker, put references to them at the bottom,
like this:

Resolves: #123
See also: #456, #789

```

Three main part:  

* header : <type> summary of commit

* body : content of commit message

* footer : issue tracker info


if you are using PyCharm IDE, you can also install this plugin `git commit template` - by damien.arrachequesne@gmail.com

## References

- [1] [Google style guide - doc Philosophy](https://github.com/google/styleguide/blob/gh-pages/docguide/philosophy.md)
- [2] [Google style guide - PyGuide](https://google.github.io/styleguide/pyguide.html)
- [3] [PEP 8](https://www.python.org/dev/peps/pep-0008/)
