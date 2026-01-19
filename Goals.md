## [Goals](#goals)

Since UTTMA is meant to be a long-term backend for any task/time, the files will need to meet these goals:

1. **Plaintext** - No format has stood the test of time longer than plain text. ASCII if possible, UTF-8 if necessary.
2. **Standard** - The plain text must be shaped as an open format; one that is so popular that most languages have a parser for it built into the standard library.
3. **Secure** - The file structure should be encrypted by default. The user should have the option to disable this, revealing the plaintext files without encryption.
4. **Scalable** - Folks are gonna grow their task lists very large. UTTMA needs to keep up with unreasonable power users. Want your to-do list to be a Terabyte? UTTMA shouldn't stop you
5. **Fast** - Clients shouldn't be bogged down just reading and writing files! Whatever this ends up being, it should be snappy.
6. **Depecatable** - This won't be perfect the first time. If, at some point in the future, something needs to be deprecated, that should be easy and straightforward.

### [A Note On the "Secure" Goal](#a-note-on-the-secure-goal)

The "Secure" goal is naturally at odds with the "Plaintext" goal, as any cyphertext is inherently not plaintext.

The "Plaintext" goal's purpose is not for the files to always exist in plain text, but for the useful data that is contained within them to be plain text. This meshes well with the "Secure" goal; once an encrypted file is decyphered, the result will be the easy-to-read-and-change plaintext.

To properly sit well with the "Standard" goal, the encryption used must be an open standard encryption. This plays well with the motivation behind both the "Plaintext" and "Standard" goals, which is that they will ensure that these files will be able to be read, modified, and transferred long into the future.

## [Choices So Far](#choices-so-far)

The current choice is to use the following:

1. **JSON** - JSON is a well-proven, open, plaintext data storage format. All major modern platforms have builtin JSON readers, and many languages include one in their standard library. This format also makes it easy to clearly separate pieces of a file's structure, allowing for deprecation as necessary.
2. **Files** - The JSON data should be the only contents of files, rather than stored in a database. This way, humans can easily pick apart their own UTTMA data, and programs can easily traverse and manipulate it.
3. **Folders** - The JSON files which contain the end data should be stored in low-hierarchy folders for both organization and quick manipulation and lookup
4. **Bundle** - The folder hierarchy should be inside a [Bundle](https://en.wikipedia.org/wiki/Bundle_\(macOS\))
5. **ISO** - The bundle should be wrapped within a file that is formatted as [an ISO image](https://en.wikipedia.org/wiki/ISO_image). This allows all UTTMA data to be passed around as a single file, while also maintaining the random access, file structure, and other capabilities of a file system. - If this does not work out as well as hoped, a non-compressed **ZIP** file can also be used.