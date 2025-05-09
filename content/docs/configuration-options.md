---
group: Configuration
weight: 10
title: Configuration Options
---
## Configuration File

All configuration options for Decap CMS are specified in a `config.yml` file, in the folder where you access the editor UI (usually in the `/admin` folder).

Alternatively, you can specify a custom config file using a link tag:

```html
<!-- Note the "type" and "rel" attribute values, which are required. -->
<link href="path/to/config.yml" type="text/yaml" rel="cms-config-url">
```

To see working configuration examples, you can [start from a template](../start-with-a-template) or check out the [CMS demo site](https://demo.decapcms.org). (No login required: click the login button and the CMS will open.) You can refer to the demo [configuration code](https://github.com/decaporg/decap-cms/blob/master/dev-test/config.yml) to see how each option was configured.

You can find details about all configuration options below. Note that [YAML syntax](https://en.wikipedia.org/wiki/YAML#Basic_components) allows lists and objects to be written in block or inline style, and the code samples below include a mix of both.

## Backend

*This setting is required.*

The `backend` option specifies how to access the content for your site, including authentication. Full details and code samples can be found in [Backends](/docs/backends-overview).

**Note**: no matter where you access Decap CMS — whether running locally, in a staging environment, or in your published site — it will always fetch and commit files in your hosted repository (for example, on GitHub), on the branch you configured in your Decap CMS config.yml file. This means that content fetched in the admin UI will match the content in the repository, which may be different from your locally running site. It also means that content saved using the admin UI will save directly to the hosted repository, even if you're running the UI locally or in staging. If you want to have your local CMS write to a local repository, try the `local_backend` setting, [currently in beta](/docs/working-with-a-local-git-repository/).

### Commit Message Templates

You can customize the templates used by Decap CMS to generate commit messages by setting the `commit_messages` option under `backend` in your Decap CMS `config.yml`.

Template tags wrapped in curly braces will be expanded to include information about the file changed by the commit. For example, `{{path}}` will include the full path to the file changed.

Setting up your Decap CMS `config.yml` to recreate the default values would look like this:

```yaml
backend:
  commit_messages:
    create: Create {{collection}} “{{slug}}”
    update: Update {{collection}} “{{slug}}”
    delete: Delete {{collection}} “{{slug}}”
    uploadMedia: Upload “{{path}}”
    deleteMedia: Delete “{{path}}”
    openAuthoring: '{{message}}'
```

Decap CMS generates the following commit types:

| Commit type     | When is it triggered?                    | Available template tags                                     |
| --------------- | ---------------------------------------- | ----------------------------------------------------------- |
| `create`        | A new entry is created                   | `slug`, `path`, `collection`, `author-login`, `author-name` |
| `update`        | An existing entry is changed             | `slug`, `path`, `collection`, `author-login`, `author-name` |
| `delete`        | An existing entry is deleted             | `slug`, `path`, `collection`, `author-login`, `author-name` |
| `uploadMedia`   | A media file is uploaded                 | `path`, `author-login`, `author-name`                       |
| `deleteMedia`   | A media file is deleted                  | `path`, `author-login`, `author-name`                       |
| `openAuthoring` | A commit is made via a forked repository | `message`, `author-login`, `author-name`                    |

Template tags produce the following output:

* `{{slug}}`: the url-safe filename of the entry changed
* `{{collection}}`: the `label_singular` or `label` of the collection containing the entry changed
* `{{path}}`: the full path to the file changed
* `{{message}}`: the relevant message based on the current change (e.g. the `create` message when an entry is created)
* `{{author-login}}`: the login/username of the author
* `{{author-name}}`: the full name of the author (might be empty based on the user's profile)

## Publish Mode

By default, all entries created or edited in the Decap CMS are committed directly into the main repository branch. The `publish_mode` option allows you to enable [Editorial Workflow](/docs/editorial-workflows/) mode for more control over the content publishing phases. You can enable the Editorial Workflow with the following line in your Decap CMS `config.yml` file:

```yaml
# /admin/config.yml
publish_mode: editorial_workflow
```

Allowed values for `publish_mode` are: `simple`, `editorial_workflow` or empty string (same as 'simple')

## Media and Public Folders

Decap CMS users can upload files to your repository using the Media Gallery. The following settings specify where these files are saved, and where they can be accessed on your built site.

### Media Folder

*This setting is required.*

The `media_folder` option specifies the folder path where uploaded files should be saved, relative to the base of the repo.

```yaml
media_folder: "static/images/uploads"
```

### Public Folder

The `public_folder` option specifies the folder path where the files uploaded by the media library will be accessed, relative to the base of the built site. For fields controlled by \[file] or \[image] widgets, the value of the field is generated by prepending this path to the filename of the selected file. Defaults to the value of `media_folder`, with an opening `/` if one is not already included.

```yaml
public_folder: "/images/uploads"
```

Based on the settings above, if a user used an image widget field called `avatar` to upload and select an image called `philosoraptor.png`, the image would be saved to the repository at `/static/images/uploads/philosoraptor.png`, and the `avatar` field for the file would be set to `/images/uploads/philosoraptor.png`.

This setting can be set to an absolute URL e.g. `https://netlify.com/media` should you wish, however in general this is not advisable as content should have relative paths to other content.

## Media Library

Media library integrations are configured via the `media_library` property, and its value should be an object with at least a `name` property. A `config` property can also be used for options that should be passed to the library in use.

**Example:**

```yaml
media_library:
  name: uploadcare
  config:
    publicKey: demopublickey
```

## Site URL

The `site_url` setting should provide a URL to your published site. May be used by the CMS for various functionality. Used together with a collection's `preview_path` to create links to live content.

**Example:**

```yaml
site_url: https://your-site.com
```

## Display URL

When the `display_url` setting is specified, the CMS UI will include a link in the fixed area at the top of the page, allowing content authors to easily return to your main site. The text of the link consists of the URL with the protocol portion (e.g., `https://your-site.com`).

Defaults to `site_url`.

**Example:**

```yaml
display_url: https://your-site.com
```

## Custom Logo

When the `logo_url` setting is specified, the CMS UI will change the logo displayed at the top of the login page, allowing you to brand the CMS with your own logo. `logo_url` is assumed to be a URL to an image file.

**Example:**

```yaml
logo_url: https://your-site.com/images/logo.svg
```

## Locale

The CMS locale.

Defaults to `en`.

Other languages than English must be registered manually.

**Example**

In your `config.yml`:

```yaml
locale: 'de'
```

And in your custom JavaScript code:

```js
import CMS from 'decap-cms-app';
import { de } from 'decap-cms-locales';

CMS.registerLocale('de', de);
```

When a translation for the selected locale is missing the English one will be used.

> When importing `decap-cms` all locales are registered by default (so you only need to update your `config.yml`).

## Show Preview Links

[Deploy preview links](../deploy-preview-links) can be disabled by setting `show_preview_links` to `false`.

**Example:**

```yaml
show_preview_links: false
```

## Search

The search functionally requires loading all collection(s) entries, which can exhaust rate limits on large repositories.
It can be disabled by setting the top level `search` property to `false`.

Defaults to `true`

**Example:**

```yaml
search: false
```

## Slug Type

The `slug` option allows you to change how filenames for entries are created and sanitized. It also applies to filenames of media inserted via the default media library.\
For modifying the actual data in a slug, see the per-collection option below.

`slug` accepts multiple options:

* `encoding`

  * `unicode` (default): Sanitize filenames (slugs) according to [RFC3987](https://tools.ietf.org/html/rfc3987) and the [WHATWG URL spec](https://url.spec.whatwg.org/). This spec allows non-ASCII (or non-Latin) characters to exist in URLs.
  * `ascii`: Sanitize filenames (slugs) according to [RFC3986](https://tools.ietf.org/html/rfc3986). The only allowed characters are (0-9, a-z, A-Z, `_`, `-`, `~`).
* `clean_accents`: Set to `true` to remove diacritics from slug characters before sanitizing. This is often helpful when using `ascii` encoding.
* `sanitize_replacement`: The replacement string used to substitute unsafe characters, defaults to  `-`.

**Example**

```yaml
slug:
  encoding: "ascii"
  clean_accents: true
  sanitize_replacement: "_"
```

## Collections

*This setting is required.*

The `collections` setting is the heart of your Decap CMS configuration, as it determines how content types and editor fields in the UI generate files and content in your repository. Each collection you configure displays in the left sidebar of the Content page of the editor UI, in the order they are entered into your Decap CMS `config.yml` file.

`collections` accepts a list of collection objects, each with the following options:

* `name` (required): unique identifier for the collection, used as the key when referenced in other contexts (like the [relation widget](../widgets/#relation))
* `identifier_field`: see detailed description below
* `label`: label for the collection in the editor UI; defaults to the value of `name`
* `label_singular`: singular label for certain elements in the editor; defaults to the value of `label`
* `description`: optional text, displayed below the label when viewing a collection
* `files` or `folder` (requires one of these): specifies the collection type and location; details in [File Collections](https://decapcms.org/docs/collection-file) and [Folder Collections](https://decapcms.org/docs/collection-folder)
* `filter`: optional filter for `folder` collections; details in [Folder Collections](https://decapcms.org/docs/collection-folder)
* `create`: for `folder` collections only; `true` allows users to create new items in the collection; defaults to `false`
* `publish`: for `publish_mode: editorial_workflow` only; `false` hides UI publishing controls for a collection; defaults to `true`
* `hide`: `true` hides a collection in the CMS UI; defaults to `false`. Useful when using the relation widget to hide referenced collections.
* `delete`: `false` prevents users from deleting items in a collection; defaults to `true`
* `extension`: see detailed description below
* `format`: see detailed description below
* `frontmatter_delimiter`: see detailed description under `format`
* `slug`: see detailed description below
* `preview_path`: see detailed description below
* `preview_path_date_field`: see detailed description below
* `fields` (required): see detailed description below
* `editor`: see detailed description below
* `summary`: see detailed description below
* `sortable_fields`: see detailed description below
* `view_filters`: see detailed description below
* `view_groups`: see detailed description below

The last few options require more detailed information.

### `identifier_field`

Decap CMS expects every entry to provide a field named `"title"` that serves as an identifier for the entry. The identifier field serves as an entry's title when viewing a list of entries, and is used in [slug](#slug) creation. If you would like to use a field other than `"title"` as the identifier, you can set `identifier_field` to the name of the other field.

**Example**

```yaml
collections:
  - name: posts
    identifier_field: name
```

### `extension` and `format`

These settings determine how collection files are parsed and saved. Both are optional—Decap CMS will attempt to infer your settings based on existing items in the collection. If your collection is empty, or you'd like more control, you can set these fields explicitly.

`extension` determines the file extension searched for when finding existing entries in a folder collection and it determines the file extension used to save new collection items. It accepts the following values: `yml`, `yaml`, `toml`, `json`, `md`, `markdown`, `html`.

You may also specify a custom `extension` not included in the list above, as long as the collection files can be parsed and saved in one of the supported formats below.

`format` determines how collection files are parsed and saved. It will be inferred if the `extension` field or existing collection file extensions match one of the supported extensions above. It accepts the following values:

* `yml` or `yaml`: parses and saves files as YAML-formatted data files; saves with `yml` extension by default
* `toml`: parses and saves files as TOML-formatted data files; saves with `toml` extension by default
* `json`: parses and saves files as JSON-formatted data files; saves with `json` extension by default
* `frontmatter`: parses files and saves files with data frontmatter followed by an unparsed body text (edited using a `body` field); saves with `md` extension by default; default for collections that can't be inferred. Collections with `frontmatter` format (either inferred or explicitly set) can parse files with frontmatter in YAML, TOML, or JSON format. However, they will be saved with YAML frontmatter.
* `yaml-frontmatter`: same as the `frontmatter` format above, except frontmatter will be both parsed and saved only as YAML, followed by unparsed body text. The default delimiter for this option is  `---`.
* `toml-frontmatter`: same as the `frontmatter` format above, except frontmatter will be both parsed and saved only as TOML, followed by unparsed body text. The default delimiter for this option is  `+++`.
* `json-frontmatter`: same as the `frontmatter` format above, except frontmatter will be both parsed and saved as JSON, followed by unparsed body text. The default delimiter for this option is  `{` `}`.

### `frontmatter_delimiter`

If you have an explicit frontmatter format declared, this option allows you to specify a custom delimiter like `~~~`. If you need different beginning and ending delimiters, you can use an array like `["(", ")"]`.

### `slug`

For folder collections where users can create new items, the `slug` option specifies a template for generating new filenames based on a file's creation date and `title` field. (This means that all collections with `create: true` must have a `title` field (a different field can be used via [`identifier_field`](#identifier_field)).

The slug template can also reference a field value by name, eg. `{{title}}`. If a field name conflicts with a built in template tag name - for example, if you have a field named `slug`, and would like to reference that field via `{{slug}}`, you can do so by adding the explicit `fields.` prefix, eg. `{{fields.slug}}`.

**Available template tags:**

* Any field can be referenced by wrapping the field name in double curly braces, eg. `{{author}}`
* `{{slug}}`: a url-safe version of the `title` field (or identifier field) for the file
* `{{year}}`: 4-digit year of the file creation date
* `{{month}}`: 2-digit month of the file creation date
* `{{day}}`: 2-digit day of the month of the file creation date
* `{{hour}}`: 2-digit hour of the file creation date
* `{{minute}}`: 2-digit minute of the file creation date
* `{{second}}`: 2-digit second of the file creation date

**Example:**

```yaml
slug: "{{year}}-{{month}}-{{day}}_{{slug}}"
```

**Example using field names:**

```yaml
slug: "{{year}}-{{month}}-{{day}}_{{title}}_{{some_other_field}}"
```

**Example using field name that conflicts with a template tag:**

```yaml
slug: "{{year}}-{{month}}-{{day}}_{{fields.slug}}"
```

### `preview_path`

A string representing the path where content in this collection can be found on the live site. This allows deploy preview links to direct to lead to a specific piece of content rather than the site root of a deploy preview.

**Available template tags:**

Template tags are the same as those for [slug](#slug), with the following exceptions:

* `{{slug}}` is the entire slug for the current entry (not just the url-safe identifier, as is the case with [`slug` configuration](#slug))
* The date based template tags, such as `{{year}}` and `{{month}}`, are pulled from a date field in your entry, and may require additional configuration - see [`preview_path_date_field`](#preview_path_date_field) for details. If a date template tag is used and no date can be found, `preview_path` will be ignored.
* `{{dirname}}` The path to the file's parent directory, relative to the collection's `folder`.
* `{{filename}}` The file name without the extension part.
* `{{extension}}` The file extension.

**Examples:**

```yaml
collections:
  - name: posts
    preview_path: "blog/{{year}}/{{month}}/{{slug}}"
```

```yaml
collections:
  - name: posts
    preview_path: "blog/{{year}}/{{month}}/{{filename}}.{{extension}}"
```

### `preview_path_date_field`

The name of a date field for parsing date-based template tags from `preview_path`. If this field is not provided and `preview_path` contains date-based template tags (eg. `{{year}}`), Decap CMS will attempt to infer a usable date field by checking for common date field names, such as `date`. If you find that you need to specify a date field, you can use `preview_path_date_field` to tell Decap CMS which field to use for preview path template tags.

**Example:**

```yaml
collections:
  - name: posts
    preview_path_date_field: "updated_on"
```

### `fields`

The `fields` option maps editor UI widgets to field-value pairs in the saved file. The order of the fields in your Decap CMS `config.yml` file determines their order in the editor UI and in the saved file.

`fields` accepts a list of collection objects, each with the following options:

* `name` (required): unique identifier for the field, used as the key when referenced in other contexts (like the [relation widget](../widgets/#relation))
* `label`: label for the field in the editor UI; defaults to the value of `name`
* `widget`: defines editor UI and inputs and file field data types; details in [Widgets](../widgets)
* `default`: specify a default value for a field; available for most widget types (see [Widgets](../widgets) for details on each widget type). Please note that field default value only works for folder collection type.
* `required`: specify as `false` to make a field optional; defaults to `true`
* `hint`: optionally add helper text directly below a widget. Useful for including instructions. Accepts markdown for bold, italic, strikethrough, and links.
* `pattern`: add field validation by specifying a list with a regex pattern and an error message; more extensive validation can be achieved with [custom widgets](../custom-widgets/#advanced-field-validation)
* `comment`: optional comment to add before the field (only supported for `yaml`)

In files with frontmatter, one field should be named `body`. This special field represents the section of the document (usually markdown) that comes after the frontmatter.

**Example:**

```yaml
fields:
  - label: "Title"
    name: "title"
    widget: "string"
    pattern: ['.{20,}', "Must have at least 20 characters"]
  - {label: "Layout", name: "layout", widget: "hidden", default: "blog"}
  - {label: "Featured Image", name: "thumbnail", widget: "image", required: false}
  - {label: "Body", name: "body", widget: "markdown"}
    comment: 'This is a multiline\ncomment'
```

### `editor`

This setting changes options for the editor view of a collection or a file inside a files collection. It has one option so far:

* `preview`: set to `false` to disable the preview pane for this collection or file; defaults to `true`

**Example:**

```yaml
  editor:
     preview: false
```

**Note**: Setting this as a top level configuration will set the default for all collections

### `summary`

This setting allows the customization of the collection list view. Similar to the `slug` field, a string with templates can be used to include values of different fields, e.g. `{{title}}`. This option over-rides the default of `title` field and `identifier_field`.

**Available template tags:**

Template tags are the same as those for [slug](#slug), with the following additions:

* `{{dirname}}` The path to the file's parent directory, relative to the collection's `folder`.
* `{{filename}}` The file name without the extension part.
* `{{extension}}` The file extension.
* `{{commit_date}}` The file commit date on supported backends (git based backends).
* `{{commit_author}}` The file author date on supported backends (git based backends).

**Example**

```yaml
    summary: "Version: {{version}} - {{title}}"
```

### `sortable_fields`

An optional list of sort fields to show in the UI.

Defaults to inferring `title`, `date`, `author` and `description` fields and will also show `Update On` sort field in git based backends.

When `author` field can't be inferred commit author will be used.

**Example**

```yaml
    # use dot notation for nested fields
    sortable_fields: ['commit_date', 'title', 'commit_author', 'language.en']
```

### `view_filters`

An optional list of predefined view filters to show in the UI.

Defaults to an empty list.

**Example**

```yaml
    view_filters:
      - label: "Alice's and Bob's Posts"
        field: author
        pattern: 'Alice|Bob'
      - label: 'Posts published in 2020'
        field: date
        pattern: '2020'
      - label: Drafts
        field: draft
        pattern: true
```

### `view_groups`

An optional list of predefined view groups to show in the UI.

Defaults to an empty list.

**Example**

```yaml
    view_groups:
      - label: Year
        field: date
        # groups items based on the value matched by the pattern
        pattern: \d{4}
      - label: Drafts
        field: draft
```
