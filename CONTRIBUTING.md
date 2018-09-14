# How to contribute

Thank you for your interest in contributing to ICON documentation project. You are very welcome to make a pull request, and create issues.

## General process

- Fork the repository on GitHub.
- For any non-trivial, significant work, please consider creating a feature branch.
- Check for unnecessary whitespace with `git diff --check` before committing.
- Push your changes to the remote branch.
- Make a pull request.

## Types of documentation contribution

- General improvements of existing documents
  - Typo correction, fixing broken refs or links, correcting inaccurate or out-of-date information, and offering better explanations through clearer writing and additional examples.
- Localization
  - Please create an issue if you are initiating a new localization. This is required to prevent any duplicate effort.
  - Partial work can be accepted. Subsequent contribution needs not create an issue.
  - It is recommended to reference the commit number of the source language file to track the changes in the source.
  - English is the default language. We welcome contributors helping us translate Korean documents into English.
  - If there are multiple language versions, English is the official document that reflects the latest changes. So, always base the English version when you generate a localized document.
  - English version should be `[filename].md`. For any other languages, please name it `[filename]-[lang].md` where `[lang]` is a 2-letter code defined in ISO-639-1. For example, `README.md` is an official version written in English, whereas `README-ja.md` is its Japanese translated version.
  - The localized doc must be stored alongside the default language doc in the same folder.
- Adding a new document that we haven’t yet covered.
  - For this type of contribution, please create an issue and describe the outline of the content. This is required to align with other ongoing documentation effort. When agreed, repository maintainer will guide you which folder you should place your document and images. Pull Request without referencing an issue can be rejected.
  - * Follow a snake case file naming convention, lower_case_with_underscore.
