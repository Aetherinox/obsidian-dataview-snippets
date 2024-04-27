<div align="center">
<h1>⭕ Contributor Guide ⭕</h1>
<br />
<p>A brief overview of contributing to this repo.</p>

<br />

</div>

<div align="center">

<!-- prettier-ignore-start -->
[![Version][badge-version-gh]][link-version-gh] [![Size][badge-size-gh]][badge-size-gh] [![Last Commit][badge-commit]][badge-commit] [![Contributors][badge-all-contributors]](#contributors-)
<!-- prettier-ignore-end -->

</div>

<br />

---

<br />

## Table of Contents

- [Table of Contents](#table-of-contents)
- [Issues, Bugs, Ideas](#issues-bugs-ideas)
- [Development](#development)
  - [Before Submitting Pull Requests](#before-submitting-pull-requests)
  - [Conventional Commit Specification](#conventional-commit-specification)
    - [Types](#types)
      - [Example 1:](#example-1)
      - [Example 2:](#example-2)
  - [Referencing Issues](#referencing-issues)
  - [Vertical alignment](#vertical-alignment)
  - [Spaces Instead Of Tabs](#spaces-instead-of-tabs)
  - [Commenting](#commenting)
  - [Casing](#casing)


<br />

---

<br />

## Issues, Bugs, Ideas
Stuff happens, and sometimes as best as we try, there may be issues with these Obsidian snippets that we are unaware of. That is the great thing about open-source; anyone can use the program and contribute to making it better.

<br />

If you have found a bug, have an issue with these snippets, or maybe even a cool idea; you can let us know by [submitting it](https://github.com/Aetherinox/obsidian-dataview-snippets/issues). However, before you submit your new issue, bug report, or feature request; head over to the [Issues Section](https://github.com/Aetherinox/obsidian-dataview-snippets/issues) and ensure nobody else has already submitted it.

<br />

Once you are sure that your issue is not already being dealt with; you may submit it by clicking [here](https://github.com/Aetherinox/obsidian-dataview-snippets/issues/new/choose). You'll be asked to specify exactly what your new submission targets, such as:
- Bug report
- Feature Suggestion

<br />

When submitting your new report, ensure you fill out any of the questions asked of you. If you do not provide enough information, we cannot help. Be as detailed as possible, and provide any logs or screenshots you may have to help us better understand what you mean. Failure to fill out the submission properly may result in it being closed without a response.

<br />

If you are submitting a bug report:

- Explain the issue in detail
- Describe how you expect for a feature to work, and what you're seeing instead of what you expected.
- Provide screenshots, logs, or anything else that can visually help track down the issue.

<br />

<div align="center">

[![Submit Issue](https://img.shields.io/badge/submit%20new%20issue-de1f5c?style=for-the-badge&logo=github&logoColor=FFFFFF&link=mailto%3Aantelle.net%40gmail.com)](https://github.com/Aetherinox/obsidian-dataview-snippets/issues)

</div>

<br />

<div align="center">

**[`^        back to top        ^`](#table-of-contents)**

</div>

<br />

---

<br />

<br />

---

<br />

## Development
If you are looking to contribute to these Obsidian snippets and submit your own code; please review this section completely. There is important information and policies provided below that you must follow for your pull request to get accepted.

The source is here for everyone to collectively share and colaborate on. If you think you have a possible solution to a problem; don't be afraid to get your hands dirty.

All contributions are made via **Pull Requests**. To make a pull request, you will need a GitHub account; if you are unclear on this process, see GitHub's documentation on forking and pull requests. 

<br />

### Before Submitting Pull Requests

- Follow the repository's code formatting conventions (see below);
- Include tests that prove that the change works as intended and does not add regressions;
- Document the changes in the code and/or the project's documentation;
- Your PR must pass the CI pipeline;
- When submitting your Pull Request, use one of the following branches:
  - For bug fixes: `main` branch
  - For features & functionality: `development` branch
- Include a proper git commit message following the [Conventional Commit Specification](https://www.conventionalcommits.org/en/v1.0.0/#specification).

<br />

If all of these items are checked, the pull request is ready to be reviewed and your pull request's label will be changed to "Ready for Review". At this point, a human will need to step in and manually verify your submission.

Once your submission has been tested and verified; it will be merged.

<br />

### Conventional Commit Specification

When commiting your changes, we require you to follow the Conventional Commit Specification, described below.

**The Conventional Commits** is a specification for the format and content of a commit message. The concept behind Conventional Commits is to provide a rich commit history that can be read and understood by both humans and automated tools. Conventional Commits have the following format:

<br />

```
<type>[(optional <scope>)]: <description>

[optional <body>]

[optional <footer(s)>]
```

#### Types
| Type | Description |
| --- | --- |
| `feat` | Introduces a new feature |
| `fix` | Bug fix for the end-user |
| `deps` | Specifically targets adding new or updating existing dependencies |
| `docs` | Change to the website or Markdown documents |
| `build` | Alters the build process. E.g: creating a new build task, updating the release script, etc. |
| `test` | Adds or refactors tests, no production code change. Usually changes the suite of automated tests for the app. |
| `perf` | Improves performance of algorithms or general execution time of the app, but does not fundamentally change an existing feature. |
| `style` | Updates or reformats the style of the source code, but does not otherwise change the way the app is implemented. Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc) |
| `refactor` | Change to production code that leads to no behavior difference, E.g. splitting files, renaming internal variables, improving code style, etc. |
| `change` | Changes to an existing feature. |
| `chore` | Includes a technical or preventative maintenance task that is necessary for managing the app or the repo, such as updating grunt tasks, but is not tied to any specific feature. Usually done for maintanence purposes. |
| `ci` | Changes related to Continuous Integration (usually `yml` and other configuration files). |
| `misc` | Anything else that doesn't fit into another commit type. Usually doesn't change production code; yet is not ci, test or chore. |
| `revert` | Revert a previous commit |
| `remove` | Removes a feature from the app. Typically features are deprecated first for a period of time before being removed. Removing a feature from the app may be considered a breaking change that will require a major version number increment. |
| `deprecate` | Deprecates existing functionality, but does not remove it from the app. |

<br />

##### Example 1:

```
feat(toc1): add ability to filter out certain headers from displaying
^───^────^  ^────────────────────────────────^
│   │       │
│   │       └───⫸ (DESC):   Summary in present tense. Use lower case not title case!
│   │
│   └───────────⫸ (SCOPE):  The package(s) that this change affects
│
└───────────────⫸ (TYPE):   See list above
```

<br />

##### Example 2:
```
<type>(<scope>): <short summary>
  │       │             │
  │       │             └─⫸ Summary in present tense. Not capitalized. No period at the end.
  │       │
  │       └─⫸ Commit Scope: animations|bazel|benchpress|common|compiler|compiler-cli|core|
  │                          elements|forms|http|language-service|localize|platform-browser|
  │                          platform-browser-dynamic|platform-server|router|service-worker|
  │                          upgrade|zone.js|packaging|changelog|docs-infra|migrations|ngcc|ve|
  │                          devtools....
  │
  └─⫸ Commit Type: build|ci|doc|docs|feat|fix|perf|refactor|test
                    website|chore|style|type|revert|deprecate
```

<br />

### Referencing Issues
If you are pushing a commit which addresses a submitted issue, reference your issue at the end of the commit message. You may also optionally add the major issue to the end of your commit body.

References should be on their own line, following the word `Ref` or `Refs`

```
Title:          fix(snippet): list of headers not rendering properly [#2002]
Description:    The description of your commit

                Ref: #2002, #3004, #3007
```

<br />

### Vertical alignment
Align similar elements vertically, to make typo-generated bugs more obvious

```typescript
let count               = 0;
const path_base         = dv.current().file.path
const path_targ         = path_base.substr(0, path_base.lastIndexOf("/"));
const path_sub          = ""

const filter_page       = '"' + path_targ + "" + path_sub + '"';
const filter_folder     = path_targ + path_sub;
```

<br />

### Spaces Instead Of Tabs
When writing your code, set your IDE to utilize **spaces**, with a configured tab size of `4 characters`.

<br />

### Commenting
Comment your code. If someone else comes along, they should be able to do a quick glance and have an idea of what is going on. Plus it helps novice readers to better understand the process.

You may use block style commenting, or single lines:

```typescript
/*
    displays 'No Results' if user has absolutely no matching headers to show
*/

if (count === 0)
{
    const rootNode = dv.el("div", "No results", { cls: "toc_results_none" });
    rootNode.setAttribute("style", "text-align:center;");
}
```

<br />

### Casing
When writing your code, ensure you stick to `camelCase`

```javascript
let myVar = 'one';
let secondVar = 'two';
```

<br />

<br />
<br />

<!-- prettier-ignore-start -->
<!-- BADGE > GENERAL -->
[link-general-npm]: https://npmjs.com
[link-general-nodejs]: https://nodejs.org
[link-npmtrends]: http://npmtrends.com/obsidian-gistr
<!-- BADGE > VERSION > GITHUB -->
[badge-version-gh]: https://img.shields.io/github/v/tag/Aetherinox/obsidian-dataview-snippets?logo=GitHub&label=Version&color=ba5225
[link-version-gh]: https://github.com/Aetherinox/obsidian-dataview-snippets/releases
<!-- BADGE > VERSION > NPMJS -->
[badge-version-npm]: https://img.shields.io/npm/v/obsidian-gistr?logo=npm&label=Version&color=ba5225
[link-version-npm]: https://npmjs.com/package/obsidian-gistr
<!-- BADGE > LICENSE -->
[badge-license-mit]: https://img.shields.io/badge/MIT-FFF?logo=creativecommons&logoColor=FFFFFF&label=License&color=9d29a0
[link-license-mit]: https://github.com/Aetherinox/obsidian-dataview-snippets/blob/main/LICENSE
<!-- BADGE > BUILD -->
[badge-build]: https://img.shields.io/github/actions/workflow/status/Aetherinox/obsidian-dataview-snippets/release-npm.yml?logo=github&logoColor=FFFFFF&label=Build&color=%23278b30
[link-build]: https://github.com/Aetherinox/obsidian-dataview-snippets/actions/workflows/release-npm.yml
<!-- BADGE > DOWNLOAD COUNT -->
[badge-downloads-gh]: https://img.shields.io/github/downloads/Aetherinox/obsidian-dataview-snippets/total?logo=github&logoColor=FFFFFF&label=Downloads&color=376892
[link-downloads-gh]: https://github.com/Aetherinox/obsidian-dataview-snippets/releases
[badge-downloads-npm]: https://img.shields.io/npm/dw/%40aetherinox%2Fmarked-alert-fa?logo=npm&&label=Downloads&color=376892
[link-downloads-npm]: https://npmjs.com/package/obsidian-gistr
<!-- BADGE > DOWNLOAD SIZE -->
[badge-size-gh]: https://img.shields.io/github/repo-size/Aetherinox/obsidian-dataview-snippets?logo=github&label=Size&color=59702a
[link-size-gh]: https://github.com/Aetherinox/obsidian-dataview-snippets/releases
[badge-size-npm]: https://img.shields.io/npm/unpacked-size/obsidian-gistr/latest?logo=npm&label=Size&color=59702a
[link-size-npm]: https://npmjs.com/package/obsidian-gistr
<!-- BADGE > COVERAGE -->
[badge-coverage]: https://img.shields.io/codecov/c/github/Aetherinox/obsidian-dataview-snippets?token=MPAVASGIOG&logo=codecov&logoColor=FFFFFF&label=Coverage&color=354b9e
[link-coverage]: https://codecov.io/github/Aetherinox/obsidian-dataview-snippets
<!-- BADGE > ALL CONTRIBUTORS -->
[badge-all-contributors]: https://img.shields.io/github/all-contributors/Aetherinox/obsidian-dataview-snippets?logo=contributorcovenant&color=de1f6f&label=contributors
[link-all-contributors]: https://github.com/all-contributors/all-contributors
[badge-tests]: https://img.shields.io/github/actions/workflow/status/Aetherinox/marked-alert-fa/npm-tests.yml?logo=github&label=Tests&color=2c6488
[link-tests]: https://github.com/Aetherinox/obsidian-dataview-snippets/actions/workflows/tests.yml
[badge-commit]: https://img.shields.io/github/last-commit/Aetherinox/obsidian-dataview-snippets?logo=conventionalcommits&logoColor=FFFFFF&label=Last%20Commit&color=313131
[link-commit]: https://github.com/Aetherinox/obsidian-dataview-snippets/commits/main/
<!-- prettier-ignore-end -->