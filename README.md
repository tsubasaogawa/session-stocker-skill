Skill moves to https://github.com/tsubasaogawa/skills

# session-stocker-skill

This skill extracts reusable knowledge from the current conversation and saves it as a Markdown note in an artifacts folder.

## Overview

This skill turns an in-progress session into something you can revisit later. Instead of preserving a chat transcript, it focuses on concise, reusable information such as conclusions, decision factors, caveats, and references.

The output directory is configured through `config.toml`.

In this repository, the skill reads `artifacts.directory` from the root `config.toml`.

```toml
[artifacts]
directory = "/path/to/your/artifacts"
```

## When to use it

Use this skill when you want to preserve the contents of a conversation for later reference, especially when you want the result saved to disk as a Markdown file.

Typical triggers:

- `このセッションをストックして`
- `この会話を残して`
- `今回の内容を artifacts に保存して`
- `セッションの知見をメモ化して`

## Generated file

The generated file uses the following format:

`<artifacts-directory>/<YYYYMMDD>_<session-summary>.md`

Here, `<artifacts-directory>` is `artifacts.directory` from `config.toml`.

### Filename rules

- Prefix the filename with the current date in `YYYYMMDD` format
- Use a short summary of the conversation in the filename
- Replace spaces with `-`
- Remove or replace unsafe filename characters such as `/`, `\\`, `:`, `*`, `?`, `"`, `<`, `>`, and `|`
- Keep the sanitized filename readable

If a file with the same name already exists, do not overwrite it. Append `-2`, `-3`, and so on instead.

### How to choose the summary name

The summary should be short, specific, and understandable without reading the full conversation.

Examples:

- `Terraform module 分割方針`
- `Python 集計スクリプト改善`
- `GitHub Actions 失敗原因調査`

## Markdown output format

The saved Markdown must always include the following sections in this order:

```md
# <session-summary>

## 概要

## 知見
```

If the session contains one or more relevant URLs, append this optional section after `知見`:

```md
## 参考情報
- https://example.com/reference
```

### Purpose of each section

#### `概要`

Summarize what the session accomplished in a short, outcome-oriented way.

#### `知見`

Record learnings and decisions that will be useful later. Use bullet points when there are multiple items.

Include:

- conclusions that were reached
- important assumptions or constraints
- patterns worth reusing
- pitfalls or caveats discovered during the session

#### `参考情報`

Record only relevant URLs that may be useful later.

Do not include file paths, command names, tool names, issue or PR numbers, or any other non-URL references.

If there are no relevant URLs, omit the entire `参考情報` section.

## Execution flow

This skill follows the steps below:

1. Review the current conversation and extract information with lasting value
2. Decide on a short summary for the session
3. Read `config.toml` and resolve `artifacts.directory`
4. Ensure the resolved output directory exists
5. Build the Markdown content in the required format, adding `参考情報` only when relevant URLs were mentioned
6. Save the file using the required naming rule
7. Tell the user the saved path and briefly summarize what was captured

## Quality bar

Before saving, make sure at least the following are true:

- the file is actually written to disk
- the filename follows the required pattern
- the content is concise and useful when revisited later
- the `知見` section contains practical takeaways rather than a transcript rewrite
- the `参考情報` section appears only when relevant URLs exist
- when `参考情報` is present, it contains URLs only

## Why this skill is useful

This skill turns an ephemeral chat into a reusable artifact. By preserving investigation results, decisions, and implementation notes as artifacts, it becomes much easier to revisit the same topic later.
