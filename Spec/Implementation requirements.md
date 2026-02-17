[HOSTESS](https://github.com/Project-HOSTESS) is, at its core, file-based object storage. That is to say, dead simple stuff which is too simple to even worry about platform/transmission differences.



## Object Storage

See: [SHELF](https://github.com/KyNorthstar/SHELF)

For HOSTESS, object storage is a bunch of files in folders organized in a way that each leaf file is the object you're looking for, whose name is an ID where its parent folders are the first characters of that ID. 

See [Serialization formats](Serialization%20formats.md) for examples!


### Topology

```
📁 Project Root
└ 📁 .objects
  ├ [object store]
  ⋮
```


### IDs

HOSTESS uses SHELF to store its data, which uses UUID as its ID system. In the filesystem, they're in their standard hex representation. However, inside the data itself, they're represented as a trimmed Base64 version.

The trimmed Base64 version of the IDs is used to reduce the size of data stored & transmitted.

This short form is created by taking the raw 128 bits of data in the UUID, converting that to a Base64 string, and then removing the trailing `==`. The trailing `==` is removed because UUIDs are constant-size, so it can be safely assumed that all of these Base64 IDs implicitly end with `==`.

Therefore, these IDs can be converted back to UUIDs by appending `==`, converting the Base64 string back into raw data, and then interpreting that raw data as a UUID however you wish, including re-converting that back into the canonical dash-separated hex string form that UUIDs are most often presented as.

In memory, do whatever you want. This spec only matters for storage & transmission.

> The [kyuuid](https://github.com/RougeWare/UuidTools#kyuuid) utility can be used to easily generate these truncated Base64 UUIDs

