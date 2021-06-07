# Contribution Guidelines

## General
Please ensure your pull request adheres to the following guidelines:

* New lessons or improvements to existing lessons are welcome.
* Please check your spelling and grammar.
* Please adhere to our [style guide](https://github.com/elixirschool/elixirschool/wiki/Lesson-Styleguide)

## A Note on Lesson Versions

All lessons should include the following front matter:

```markdown
---
version: 2.0.0
title: Ecto
---
```

Change the `version` attribute according to the following rules:

* MAJOR — You (re)wrote the whole thing. Your new content will need some translation.
* MINOR — Added or removed some content, few sentences, etc.
* PATCH — Spelling, typos. Probably not translated stuff.

Fun fact! The version changes are necessary because we use that to programmatically determine and inform translators of new content that requires translation.

Each language has a generated [Translation Report](https://elixirschool.com/es/report/)

## Adding a New Lesson
To add a new lesson, create the file under the appropriate directory in `en/lessons` (or `<language_code>/lessons`) if you are not writing your new lesson in English).

Then, update `_data/content.yml` with the name of your new lesson under the appropriate section.

If you've added a new section (i.e. a new directory under `/lessons`), add the section name under the `sections` key of `_data/locales/en.yml` or `_data/locales/<language_code>.yml` if the section + lesson you added are not in English.

Thank you for your contributions!


## Adding a new Language

1. Create a folder using the ISO language code (e.g. ja, zh-hans, es, et al) with lesson subfolders.
Not sure which language code to use?
Check [here](https://www.loc.gov/standards/iso639-2/php/English_list.php) for the official list.

  ```shell
  $ cd elixirschool
  $ mkdir -p ja/lessons/{basics,advanced,specifics,libraries}
  $ touch ja/lessons/{basics,advanced,specifics,libraries}/.gitkeep
  ```

1. Add your language code to `interlang` in `_data/locales/en.yml`:

  ```yaml
  interlang:
   ja: Japanese
  ```

1. Create a locale file for your new language using `_data/locales/en.yml` as a guide:

  ```shell
  $ touch _data/locales/ja.yml
  ```

1. If the new language is RTL (right-to-left) it should be added to the `rtl_languages` list in `config.yml`:

  ```yaml
  script_direction: rtl
  ```

## Gotcha

Look out for Liquid templating weirdness!

If you have a code snippet that includes the following syntax: `{%{message: "error message"}, :error}`, i.e. if you have a tuple where the first element is a map, WATCH OUT! That set of characters, `{%` is the start of a liquid tag! Instead, wrap your backticked code block in `{% raw % }` `{% endraw %}`
