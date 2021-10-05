# Readme Generator For Helm

This tool is forked from
[bitnami labs](https://github.com/bitnami-labs/readme-generator-for-helm).
Please see their README for more detailed information. This fork adds the
ability to specify an environment variable column and also adds a `check`
modifier to inform users to visit the values.yaml for the default value of more
involved parameters.

## TLDR

Install this tool by cloning the repo and install it with npm

```
git clone https://github.com/bitnami-labs/readme-generator-for-helm.git
npm install -g readme-generator-for-helm
```

It can be run with environment variable support

```
readme-generator -r README.md -v values.yaml -e
```

or without

```
readme-generator -r README.md -v values.yaml
```

If you use the environment variable flag, you just need to specify an additional
argument for each respective parameter, i.e.

```
## @param my-custom-param.foo MY_CUSTOM_FOO_VAR [optional modifier] My foo parameter description
## @param my-custom-param.bar MY_CUSTOM_BAR_VAR [optional modifier] My bar parameter description
##
my-custom-param:
  foo: bar
  bar: baz
```

If your parameter isn't associated with an environment variable, just use `N/A`

```
## @param my-custom-param.foo N/A [optional modifier] My foo parameter description
## @param my-custom-param.bar N/A [optional modifier] My bar parameter description
##
my-custom-param:
  foo: bar
  bar: baz
```

The modifiers will affect what is added to the default value column for params
that don't necessarily have a default value, or that have a value too
complicated for a simple markdown table cell. The available modifiers are
`array`, `object`, `string`, or `check`.

---

Autogenerate Helm Charts READMEs' tables based on values YAML file metadata.

## How it works

The tools expects some metadata for the descriptions in the provided
`values.yaml` file. It will parse and check the metadata against the real
values. If the metadata is consistent with the real values, it will generate and
insert the values table into the provided `README.md` file. If the metadata is
not correct it will print the full list of errors. It checks whether there are
metadata for non existing values or there are missing metadata for an existing
value.

The table that will be inserted into the `readme.md` will have the following
structure:

```
## Parameters

### Section 1 title

| Name      | Description             | Default        |
|:----------|:------------------------|:---------------|
| `value_1` | Description for value 1 | `defaultValue` |
| `value_2` | Description for value 2 | `defaultValue` |
| `value_3` | Description for value 3 | `defaultValue` |

### Section 2 title

| Name      | Description             | Default        |
|:----------|:------------------------|:---------------|
| `value_1` | Description for value 1 | `defaultValue` |
| `value_2` | Description for value 2 | `defaultValue` |
| `value_3` | Description for value 3 | `defaultValue` |

...
```

The number of `#` characters needed for the sections title is dynamically
calculated, and the title of the `Parameters` section can be configured via the
[configuration file](#configuration-file).

## Requirements

The project has been developed and tested with node version `12.21.0`.

## Install

Execute the following commands to install the tool:

```
git clone https://github.com/bitnami-labs/readme-generator-for-helm
npm install -g readme-generator-for-helm
```

## Test

We use [Jest](https://jestjs.io) to implement the tests. In order to test your
changes execute the following command:

```
npm run-script test
```

## Basic usage

```
Usage: readme-generator [options]

Options:
  -r, --readme <path>   Path to the README.md file
  -v, --values <path>   Path to the values.yaml file
  -c, --config <path>   Path to the config file
  -m, --metadata <path> Path to the metadata file
  -h, --help            display help for command
```

## Metadata

For the tool to work, you need to add some metadata to your `values.yaml` file.

By default we use a format similar to Javadoc, using `@xxx` for tags followed by
the tag structure.

The following are the tags supported at this very moment:

-   For a parameter: `## @param (fullKeyPath) [modifier?] Description`.
-   For a section: `## @section Section Title"`.
-   To skip an object and all its childrens: `## @skip fullKeyPath`.

All the tags as well as the two initial `#` characters for the comments style
can be configured in the [configuration file](#configuration-file).

> IMPORTANT: tags' order or position in the file is NOT important except for the
> @section tag. The @section that will include in the section all the parameters
> after it until a new section is found or the file ends.

The `modifier` is optional and it will let you override the value of an object.
Currently there two modifiers supported:

-   `[array]` Indicates that the value of the parameter must be set to `[]`.
-   `[object]` Indicates that the value of the parameter must be set to `{}`.
-   `[string]` Indicates that the value of the parameter must be set to `""`.
-   `[check]` Indicates that the value of the parameter can be obtained in the
    chart's `values.yaml` file, useful for long strings or arrays.

The modifiers are also customizable via the
[configuration file](#configuration-file).

## Configuration file

The configuration file has the following structure:

```
{
  "comments": {
    "format": "##"                       <-- Which is the comments format in the values YAML
  },
  "tags": {
    "param": "@param",                   <-- Tag that indicates a parameter
    "section": "@section",               <-- Tag that indicates a section
    "skip": "@skip"                      <-- Tag that indicates the object must be skipped
  },
  "modifiers": {
    "array": "array",                    <-- Modifier that indicates an array type
    "object": "object"                   <-- Modifier that indicates an object type
    "string": "string"                   <-- Modifier that indicates an string type
    "check": "check"                     <-- Modifier that indicates to check the real value at the values.yaml
  },
  "regexp": {
    "paramsSectionTitle": "Parameters"   <-- Title of the parameters section to replace in the README.md
  }
}
```

## Generate values.yaml Schema

The readme-generator-for-helm can also use the metadata in the `values.yaml` to
generate an schemaObject as output by using the `--metadata` option. File
generated will be a JSON file formated according to the
[OpenAPIv3 SchemaObject](https://spec.openapis.org/oas/v3.1.0#schema-object)
definition.
