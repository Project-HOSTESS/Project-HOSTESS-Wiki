[HOSTESS](https://github.com/Project-HOSTESS) is, at its core, file-based object storage. That is to say, dead simple stuff which is too simple to even worry about platform/transmission differences.



## Object Storage

See: Project SHELF

For HOSTESS, object storage is a bunch of files in folders organized in a way that each leaf file is the object you're looking for, whose name is an ID where its parent folders are the first characters of that ID. Keep reading for examples!

See also: [SHELF](https://github.com/KyNorthstar/SHELF)

### Topology

```
📁 Project Root
└ 📁 .objects
  ├ [object store]
  ⋮
```


### IDs

As you can see, HOSTESS uses UUID as its ID system. In the filesystem, they're in their standard hex representation. However, inside the data itself, they're represented as a trimmed Base64 version.

The trimmed Base64 version of the IDs is used to reduce the size of data stored & transmitted.

This short form is created by taking the raw 128 bits of data in the UUID, converting that to a Base64 string, and then removing the trailing `==`. The trailing `==` is removed because UUIDs are constant-size, so it can be safely assumed that all of these Base64 IDs implicitly end with `==`.

Therefore, these IDs can be converted back to UUIDs by appending `==`, converting the Base64 string back into raw data, and then interpreting that raw data as a UUID however you wish, including re-converting that back into the canonical dash-separated hex string form that UUIDs are most often presented as.

In memory, do whatever you want. This spec only matters for storage & transmission.

> The [kyuuid](https://github.com/RougeWare/UuidTools#kyuuid) utility can be used to 


### Content format

Always and forever, HOSTESS object files is be formatted as JSON content, and content versioning is specified as a SEMVER:

```json
{
    "_v": "1.0.0",
    ...metadata.
    
    "_c":
	{
		...content
    }
}
```

where `...metadata` is key-value pairs which can change based on the format version, such as a type specifier; `...content` is key-value pairs which constitute the actual user data in the object, whose format is arbitrary but might have its own format version/scheme. Future versions may or may not tie the top-level format specifier to the content.

This is what will **always** be required of any HOSTESS object file: a top-level `_v` key pointing to a SEMVER-formatted string specifying the format version of the file, some metadata on that same level, and the content nested in its own object keyed by `_c`.

Here's a table of each top-level key that will always be required in a HOSTESS object:

|  key | meaning            |
|-----:|:-------------------|
| `_v` | Version of format  |
| `_c` | Contents of object |

> Of course, in production, there won't be unnecessary whitespace in these files


#### Example

As an example of what a real object might look like, here's a HOSTESS task:

```json
{"_v":"0.1.0","t":"task","_c":{"body":"Clean the basement","parent":"2ODRYEVpRruVPMysmQXBcA","tags":["8cNk7mE1SHygp0NSIBdqjg","v/h4G6JoQ++YWX67VFIWxQ","b3rdNT48TbGcdP6uxRG83g"],"state":"Complete"}}
```
or, expanded:
```json
{
   "_v": "0.1.0",
   "t": "task",
   "_c":
   {
       "body": "Clean the basement",
       "parent": "2ODRYEVpRruVPMysmQXBcA",
       "tags": [
	       "8cNk7mE1SHygp0NSIBdqjg",
	       "v/h4G6JoQ++YWX67VFIWxQ",
	       "b3rdNT48TbGcdP6uxRG83g"
       ],
       "state": "Complete"
   }
}
```

In this example, the `"t": "task"` field and the entire contents of the `"_c"` field are version-dependent and might change (and need migration) in future versions.