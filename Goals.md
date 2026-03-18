## [Goals](#goals)

Since HOSTESS is meant to be a long-term backend for any task, the files will need to meet these goals:

1. **Portable** - No lock-in at all. Everything must be easy to take out of HOSTESS and put somewhere else.
2. **Plaintext** - No file format has stood the test of time longer than plain text
3. **Standard** - The plain text must be shaped as an open format; one that is so popular that most languages have a parser for it built into the standard library.
4. **Secure** - In-flight encryption should be enforced as required. Clients are encouraged to encrypt at-rest as well.
5. **Scalable** - Folks are gonna grow their task lists very large. HOSTESS needs to keep up with unreasonable power users. Want to only have one to-do list which takes up a Terabyte? HOSTESS shouldn't stop you.
6. **Fast** - Clients shouldn't be bogged down just reading and writing files! Whatever this ends up being, it should be snappy.
7. **Updatable** - This won't be perfect the first time. If, at some point in the future, something we aren't thinking of today needs to be deprecated or introduced, that should be easy and straightforward.
8. **FOSS** - Developed and maintained in the open, fully free for anyone to use, hack, fork, and contribute to.

### [A Note On the "Secure" Goal](#a-note-on-the-secure-goal)

The "Secure" goal is naturally at odds with the "Plaintext" goal, as any cyphertext is inherently not plaintext.

The "Plaintext" goal's purpose is not for the files to always exist in plain text, but for the useful data that is contained within them to be plain text, as opposed to some binary format. In that sense, the "Platintext" goal actually meshes well with the "Secure" goal; once an encrypted file is decrypted, the result will be the easy-to-read-and-change plaintext, which is straightforward to encrypt again.

To properly sit well with the "Standard" goal, the encryption used must be an open standard encryption. This plays well with the motivation behind both the "Plaintext" and "Standard" goals, which is that they will ensure that these files will be able to be read, modified, and transferred long into the future.

## [Choices So Far](#choices-so-far)

The current choice is to use the following:

1. **UTF-8** - UTF-8 is not only a well-received and broadly-supported plaintext format with support for all currently-written non-esoteric languages, it's also backwards-compatible with ASCII, meaning as long as a UTF-8 file only contains characters present in the original 7-bit ASCII set, it can be viewed/edited by very old and basic software.
2. **JSON** - JSON is a well-proven, open, plaintext data storage format. All major modern platforms have builtin JSON readers, and most modern languages include one in their standard library. This format also makes it easy to clearly separate pieces of a file's structure, allowing for deprecation as necessary (For example, one might imagine: "the use of the `"version"` field is deprecated in favor of the `"format-version"` and `"content-version"` fields").
3. **Object Store** - The JSON data which represents a HOSTESS object should be the only content of the file that represents that object, rather than using an ORM database like SQL. This way, anyone can easily pick apart their own HOSTESS data, and programs can easily traverse and manipulate it.
4. **Folders** - The JSON files which contain the end data should be stored in low-hierarchy folders for both organization and quick manipulation and lookup
5. **Bundle** - The folder hierarchy should be inside a [Bundle](https://en.wikipedia.org/wiki/Bundle_\(macOS\))
6. **ISO** - The bundle should be wrapped within a file that is formatted as [an ISO image](https://en.wikipedia.org/wiki/ISO_image). This allows all UTTMA data to be passed around as a single file, while also maintaining the random access, file structure, and other capabilities of a file system. - If this does not work out as well as hoped, a non-compressed **ZIP** file can also be used.