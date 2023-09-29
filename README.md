<p align="center">
  <img width="30%" alt="BRImage" src="assets/astro-group-bristol.png">
</p>

# Astro Group Bristol Guidelines

This repository contains some general information and coding practices within the organization.

<!--BEGIN TOC-->

## Table of Contents

1. [In this repository](#in-this-repository)
   1. [Guides](#guides)
2. [Creating new repositories](#creating-new-repositories)
3. [GitHub Workflows](#github-workflows)
4. [Questions and Queries](#questions-and-queries)

<!--END TOC-->

<hr/>

## In this repository <a id="toc-tag-mdtoc" name="in-this-repository"></a>

This repository contains some (hopefully useful) guides, and general etiquette for our organization.

### Guides <a id="toc-tag-mdtoc" name="guides"></a>

- See [Guides](./guides)

## Creating new repositories <a id="toc-tag-mdtoc" name="creating-new-repositories"></a>

All members have default read permissions for repositories, however this still allows you to create new repositories under the organization. Repositories you create are controlled by _you_, and are managed similarly to how you would manage your own personal repositories.

Collaborators need to be invited, even if they are members of the Astro Group Bristol.

Creating repositories for your own code is perfectly acceptable always. If you wish to create a public repository for the group, e.g. `short-data-analysis-scripts` for dumping commonly used code snippets, please discuss with the other members beforehand to gauge the interest and use.

You can create a new `astro-group-bristol` repository by navigating to the organization page ([https://github.com/astro-group-bristol](https://github.com/astro-group-bristol)), and clicking on "New".

![create-new-repo](assets/create-new-repo.png)

## GitHub Workflows <a id="toc-tag-mdtoc" name="github-workflows"></a>

We have 3000 free minutes of GitHub Workflows per month. Please configure workflows to use minimal time if possible. This can be done by

- only triggering a work flow on specific branches:

```yaml
# only on main branch example
on:
  push:
    branches:
      - main
```

- only on pull-requests

```yaml
# only when pull request to main example
on:
  pull_request:
    branches:
      - main
```

- only running tests if code files have been changed

```yaml
# python file example
on:
  push:
    paths:
      # only run if python files in `src` have been modified
      - "src/**.py"
```

## Questions and Queries <a id="toc-tag-mdtoc" name="questions-and-queries"></a>

Please direct questions and queries to any members of the organization, or post them in the Astro Group Bristol Slack.
