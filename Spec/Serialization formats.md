## SHELF

HOSTESS stores all its data object-store style using [SHELF](https://github.com/KyNorthstar/SHELF).



## Basic storage format concepts

Each HOSTESS object is stored as a separate SHELF object in plaintext JSON format.


### Content format

_**Always and forever**_, HOSTESS object files are formatted as JSON, and content versioning is specified as a [Semver](https://semver.org), like this:

```json
{
    "_v": "0.1.0",
    ...metadata,
    
    "_c":
	{
		...content
    }
}
```

where `...metadata` is key-value pairs which can change based on the format version, such as a type specifier. Similarly, `...content` is key-value pairs which constitute the actual user data in the object, whose format is arbitrary but might have its own format version/scheme. Future versions may or may not tie the top-level format specifier to the content.

This is what will **always** be required of any HOSTESS object file: 
- a top-level `_v` key whose value is a Semver (string) specifying the format version of the file, the content nested in its own object, keyed by `_c`.

HOSTESS also allows for some additional metadata on the top level, as long as those keys don't start with `_`.

Here's a table of each top-level key that will _always_ be required in a HOSTESS object:

|  key | meaning                |
|-----:|:-----------------------|
| `_v` | Version of the format  |
| `_c` | Contents of the object |

> Of course, in production, there won't be unnecessary whitespace in these files


### First-party fields

HOSTESS's own fields are plain words (in lower-camel-case), like `"body"`, `"completionPercentage"`, `"tags"`, etc.

These are specified within this document.


### Third-party fields

Anyone can add custom fields to HOSTESS objects, with only naming restrictions.

Each third-party field MUST start with a period (`.`) and then a lowercase reverse-domain-name organization identifier, and then the field name. The field name can be anything, without restriction. Even spaces are allowed.

```
.<org id>.<field>
```

For example, if Blue Husky Studios (at BHStudios.org) wants to add pre-rendered TTS to each task, and save within the ask JSON an ID pointing to that TTS data, that field might look like this:

```json
".org.bhstudios.tts cache": "3wR46UMpTIaTL1nRwVbTiw"
```


#### Example

As an example of what a real object might look like, here's a HOSTESS task:

```json
{"_v":"0.1.0","id":"DJX/DxC/R+SCAEfCEnVogQ","t":"task","_c":{"body":"Clean the basement","parent":"2UFSeNaLR7uR5tGFWv8agQ","tags":["8cNk7mE1SHygp0NSIBdqjg","v/h4G6JoQ++YWX67VFIWxQ","b3rdNT48TbGcdP6uxRG83g"],"state":"Complete"}}
```
or, expanded:
```json
{
   "_v": "0.1.0",
   "id": "DJX/DxC/R+SCAEfCEnVogQ",
   "t": "task",
   "_c":
   {
       "body": "Clean the basement",
       "parent": "2UFSeNaLR7uR5tGFWv8agQ",
       "tags": [
	       "8cNk7mE1SHygp0NSIBdqjg",
	       "v/h4G6JoQ++YWX67VFIWxQ",
	       "b3rdNT48TbGcdP6uxRG83g"
       ],
       "state": "Complete"
   }
}
```

In this example, the `"t"` and `"id"` fields (and the contents of the `"_c"` field) are version-dependent and might change (and need migration) in future versions.

Adding the above custom TTS field example:

```json
{
   "_v": "0.1.0",
   "id": "DJX/DxC/R+SCAEfCEnVogQ",
   "t": "task",
   "_c":
   {
       "body": "Clean the basement",
       "parent": "2UFSeNaLR7uR5tGFWv8agQ",
       "tags": [
	       "8cNk7mE1SHygp0NSIBdqjg",
	       "v/h4G6JoQ++YWX67VFIWxQ",
	       "b3rdNT48TbGcdP6uxRG83g"
       ],
       "state": "Complete"
   },
   ".org.bhstudios.tts cache": "3wR46UMpTIaTL1nRwVbTiw"
}
```



## Tasks
> Format Version 0.1.0

```
{
	"_v": semver,
	"id": SHELF ID,
	"t": "task",
	"_c":
	{
		"body": markdown,
		"notes": optional markdown,
		"parent": SHELF ID,
		"subtasks": optional [
			SHELF ID,
			...
		],
		"tags": optional [
			SHELF ID,
			...
		],
		"state": "open" | "complete" | "dropped",
		"completionPercentage": optional float (0.0 ... 1.0)
	}
}
```

### JSON Schema
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://example.com/hostess-task.schema.json",
  "title": "HOSTESS Task Object",
  "type": "object",
  "properties": {
    "_v": {
      "type": "string",
      "pattern": "^\\d+\\.\\d+\\.\\d+$",
      "description": "Semantic version"
    },
    "id": {
      "type": "string",
      "pattern": "^[A-Za-z0-9+/]{22}$",
      "description": "SHELF ID (Base64-encoded UUID without trailing ==)"
    },
    "t": {
      "type": "string",
      "const": "task"
    },
    "_c": {
      "type": "object",
      "properties": {
        "body": {
          "type": "string",
          "description": "Markdown-formatted task description"
        },
        "parent": {
          "type": "string",
          "pattern": "^[A-Za-z0-9+/]{22}$",
          "description": "SHELF ID of parent object"
        },
        "subtasks": {
          "type": "array",
          "items": {
            "type": "string",
            "pattern": "^[A-Za-z0-9+/]{22}$"
          },
          "description": "Array of SHELF IDs for subtasks"
        },
        "tags": {
          "type": "array",
          "items": {
            "type": "string",
            "pattern": "^[A-Za-z0-9+/]{22}$"
          },
          "description": "Array of SHELF IDs for tag objects"
        },
        "state": {
          "type": "string",
          "enum": ["open", "complete", "dropped"]
        },
        "notes": {
          "type": "string",
          "description": "Markdown-formatted notes"
        },
        "completionPercentage": {
          "type": "number",
          "minimum": 0,
          "maximum": 1
        }
      },
      "required": ["body", "parent", "state"]
    }
  },
  "required": ["_v", "id", "t", "_c"]
}
```

### Examples

```json
{
	"_v": "0.1.0",
	"id": "DJX/DxC/R+SCAEfCEnVogQ",
	"t": "task",
	"_c":
	{
		"body": "Clean the basement",
		"notes": "Gotta get this done _before_ the party!",
		"parent": "2UFSeNaLR7uR5tGFWv8agQ",
		"tags": [
			"8cNk7mE1SHygp0NSIBdqjg",
			"v/h4G6JoQ++YWX67VFIWxQ",
			"b3rdNT48TbGcdP6uxRG83g"
		],
		"subtasks": [
			"bv2QsHG3T/GO130TWMZ94g",
			"TpufnodkRzGi7IoGI9tkNQ",
			"XBpQJHekQBmANbarSRt1gg",
			"5djb6WR0S66LnqyYC8f37w",
			"jCXkIzwqSn6ttwu+ED02XA",
			"WH5sk1FVTLyJE7+HAHfUsA",
			"6MKpCtbVRR2vQmTC4BzwDA"
		],
		"state": "open",
		"completionPercentage": 0.33333333333333331
	}
}
```

```json
{
	"_v": "0.1.0",
	"id": "8jVsNvvmSLmewisfGvgQPQ",
	"t": "task",
	"_c":
	{
		"body": "Buy milk",
		"parent": "zO671XWCQF68pM1oXprpYA",
		"state": "complete"
	}
}
```



## Tasklists
> Format Version 0.1.0

```
{
	"_v": semver,
	"id": SHELF ID,
	"t": "tasklist",
	"_c":
	{
		"name": string,
		"notes": optional markdown,
		"tasks": [
			SHELF ID,
			...
		],
		"parentGroups": optional [
			SHELF ID,
			...
		],
		"tags": optional [
			SHELF ID,
			...
		],
		"state": "open" | "complete" | "dropped",
		"completionPercentage": optional float (0.0 ... 1.0)
	}
}
```

### JSON Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://example.com/hostess-tasklist.schema.json",
  "title": "HOSTESS Tasklist Object",
  "type": "object",
  "properties": {
    "_v": {
      "type": "string",
      "pattern": "^\\d+\\.\\d+\\.\\d+$",
      "description": "Semantic version"
    },
    "id": {
      "type": "string",
      "pattern": "^[A-Za-z0-9+/]{22}$",
      "description": "SHELF ID (Base64-encoded UUID without trailing ==)"
    },
    "t": {
      "type": "string",
      "const": "tasklist"
    },
    "_c": {
      "type": "object",
      "properties": {
        "name": {
          "type": "string",
          "description": "Tasklist name"
        },
        "notes": {
          "type": "string",
          "description": "Markdown-formatted notes"
        },
        "tasks": {
          "type": "array",
          "items": {
            "type": "string",
            "pattern": "^[A-Za-z0-9+/]{22}$"
          },
          "description": "Array of SHELF IDs for task objects"
        },
        "parentGroups": {
          "type": "array",
          "items": {
            "type": "string",
            "pattern": "^[A-Za-z0-9+/]{22}$"
          },
          "description": "Array of SHELF IDs for parent group objects"
        },
        "tags": {
          "type": "array",
          "items": {
            "type": "string",
            "pattern": "^[A-Za-z0-9+/]{22}$"
          },
          "description": "Array of SHELF IDs for tag objects"
        },
        "state": {
          "type": "string",
          "enum": ["open", "complete", "dropped"]
        },
        "completionPercentage": {
          "type": "number",
          "minimum": 0,
          "maximum": 1
        }
      },
      "required": ["name", "tasks", "state"]
    }
  },
  "required": ["_v", "id", "t", "_c"]
}
```

### Examples

```json
{
   "_v": "0.1.0",
   "id": "2UFSeNaLR7uR5tGFWv8agQ",
   "t": "tasklist",
   "_c": {
       "name": "Home Maintenance",
       "notes": "Quarterly tasks for keeping the house in shape",
       "tasks": [
           "DJX/DxC/R+SCAEfCEnVogQ",
           "XjKKSm+YSiC9pk4LU3Jstw",
           "G8yxzvsvRBK4I6PAP/wkBA"
       ],
       "parentGroups": [
           "oo0Yd5mKQgyEB3SzBEdyQQ"
       ],
       "tags": [
           "8cNk7mE1SHygp0NSIBdqjg",
           "v/h4G6JoQ++YWX67VFIWxQ"
       ],
       "state": "open",
       "completionPercentage": 0.6666666666666666
   }
}
```

```json
{
   "_v": "0.1.0",
   "id": "zO671XWCQF68pM1oXprpYA",
   "t": "tasklist",
   "_c": {
       "name": "Grocery Shopping",
       "tasks": [
           "8jVsNvvmSLmewisfGvgQPQ"
       ],
       "state": "complete"
   }
}
```



## Tags
> Format Version 0.1.0

```
{
	"_v": semver,
	"id": SHELF ID,
	"t": "tag",
	"_c":
	{
		"label": string
	}
}
```


### JSON Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://example.com/hostess-tag.schema.json",
  "title": "HOSTESS Tag Object",
  "type": "object",
  "properties": {
    "_v": {
      "type": "string",
      "pattern": "^\\d+\\.\\d+\\.\\d+$",
      "description": "Semantic version"
    },
    "id": {
      "type": "string",
      "pattern": "^[A-Za-z0-9+/]{22}$",
      "description": "SHELF ID (Base64-encoded UUID without trailing ==)"
    },
    "t": {
      "type": "string",
      "const": "tag"
    },
    "_c": {
      "type": "object",
      "properties": {
        "label": {
          "type": "string",
          "description": "Tag label"
        }
      },
      "required": ["label"]
    }
  },
  "required": ["_v", "id", "t", "_c"]
}
```


### Examples

```json
{
   "_v": "0.1.0",
   "id": "8cNk7mE1SHygp0NSIBdqjg",
   "t": "tag",
   "_c": {
       "label": "Urgent"
   }
}
```

```json
{
   "_v": "0.1.0",
   "id": "v/h4G6JoQ++YWX67VFIWxQ",
   "t": "tag",
   "_c": {
       "label": "Home"
   }
}
```

```json
{
   "_v": "0.1.0",
   "id": "b3rdNT48TbGcdP6uxRG83g",
   "t": "tag",
   "_c": {
       "label": "Weekend Project"
   }
}
```