# <a name="coding-standards"></a>Coding standards example 1
When contributing code for this repository, please follow the guidelines below:
### Git workflow
- Follow the [Git Feature Branch Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow). The master branch preserves the development history with no broken code. When working on a system feature, create a separate feature branch.
- Always create a pull request before merging the feature branch into the main branch. Doing so helps keep track of the project history and manage git issues.
- NEVER perform git rebasing on public branches, which means that you should not run "git rebase [FEATURE-BRANCH]" while you are on a public branch (e.g., the main branch). Doing so will badly confuse other developers since rebasing rewrites the git history, and other people's works may be based on the public branch. Check [this tutorial](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing) for details.
- NEVER push credentials to the repository, for example, database passwords or private keys for signing digital signatures.
- Request a code review when you are not sure if the feature branch can be safely merged into the main branch.
### Python package installation
- Make sure you are in the correct conda environment before installing packages. Otherwise, the packages will be installed to the server's general python environment, which can be problematic.
- Make sure the packages are in the [install_packages.sh](/back-end/install_packages.sh) script with version numbers, which makes it easy for others to install packages.
- Use the pip command first. Only use the conda command to install packages when the pip command does not work.
### Coding style
- Use the functional programming style (check [this Python document](https://docs.python.org/3/howto/functional.html) for the concept). It means that each function is self-contained and does NOT depend on a state that may change outside the function (e.g., global variables). Avoid using the object-oriented programming style unless necessary. In this way, we can accelerate the development progress while maintaining code reusability.
- Minimize the usage of global variables, unless necessary, such as system configuration variables. In this way, each function can be independent, which is good for debugging code and assigning coding tasks to a specific collaborator.
- Use a consistent coding style.
  - For Python, follow the [PEP 8 style guide](https://www.python.org/dev/peps/pep-0008/), for example, putting two blank lines between functions, using the lower_snake_case naming convention for variable and function names. Please use double quote (not single quote) for strings.
  - For JavaScript, follow the [Idiomatic JavaScript style guide](https://github.com/rwaldron/idiomatic.js), for example, using lowerCamelCase naming convention for variable and function names. Please use double quote (not single quote) for strings.
- Document functions and script files using docstrings.
  - For Python, follow the [numpydoc style guide](https://numpydoc.readthedocs.io/en/latest/format.html). Here is an [example](https://numpydoc.readthedocs.io/en/latest/example.html#example). More detailed numpydoc style can be found on [LSST's docstrings guide](https://developer.lsst.io/python/numpydoc.html).
  - For JavaScript, follow the [JSDoc style guide](https://jsdoc.app/index.html)
- For naming files, never use white spaces.
  - For Python script files (and shell script files), use the lower_snake_case naming convention. Avoid using uppercase.
  - For JavaScript files, use the lower_snake_case naming convention. Avoid using uppercases.
- Always comment your code, which helps others read the code and reduce our pain in the future when debugging or adding new features.
