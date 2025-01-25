# Contributing

The Parosly uses GitHub to manage and review pull requests.

* If you are a new contributor see: [Steps to Contribute](#steps-to-contribute)

* If you have a minor fix or enhancement, feel free to submit a pull request.
* Be sure to sign off on the [DCO](https://github.com/probot/dco#how-it-works).

## Steps to Contribute

If you'd like to tackle an issue, please claim it by commenting on the GitHub issue to indicate your intention to work on it. This helps avoid multiple contributors working on the same issue simultaneously.

To run the project locally you may need to install some prerequisites:
- [Hugo](https://github.com/gohugoio/hugo) (v0.141.0-extended or higher)
- [Node.js](https://nodejs.org/) (v14.17.0 or higher)
- [npm](https://www.npmjs.com/) (v6.14.13 or higher)

Once you have the prerequisites installed, you can run the project locally and test your changes by following these steps:
```bash
$ git clone https://github.com/parosly/website.git
$ cd website
$ npm install
$ npm run dev (or hugo server)
```

## Pull Request Checklist

* Branch from the main branch and, if needed, rebase to the current main branch before submitting your pull request. If it doesn't merge cleanly with main you may be asked to rebase your changes.
* PR descriptions should contain comprehensive descriptions of all commits introduced by the appropriate PR. You can find and example [here](https://github.com/parosly/website/pull/1).