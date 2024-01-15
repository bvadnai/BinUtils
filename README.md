# BinUtils
Swift functions to ease working with binary formats

**Installation**

Either copy `BinUtils.swift` into your project, or use Swift Package Manager by adding the following dependency to your `Package.swift`:

    dependencies: [
        .Package(url: "https://github.com/bvadnai/BinUtils.git", majorVersion: 0, minor: 1),
    ]

**hexlify(data)**

- return the hexadecimal representation of the binary data
- akin to [hexlify() in Python binascii module](https://docs.python.org/2/library/binascii.html#binascii.hexlify)
- for iOS and OS X

```swift
let data = "Hello".dataUsingEncoding(.utf8)!
assert(hexlify(data) == "48656C6C6F")
```

**unhexlify(hexstr)**

- return the binary data represented by the hexadecimal string hexstr
- akin to [unhexlify() in Python binascii module](https://docs.python.org/2/library/binascii.html#binascii.unhexlify)

```swift
let s = String(data: unhexlify("48656C6C6F")!, encoding: .utf8)
assert(s == "Hello")
```

**pack(fmt, [v1, v2, ...])**

- return data containing the values v1, v2, ... packed according to the given format
- akin to [pack() in Python struct module](https://docs.python.org/2/library/struct.html#struct.pack)

```swift
let d = pack("<h2I3sf", [1, 2, 3, "asd", 0.5])
assert(d == unhexlify("0100 02000000 03000000 617364 0000003f"))
```

**unpack(fmt, string)**

- unpack the data according to the given format
- akin to [unpack() in Python struct module](https://docs.python.org/2/library/struct.html#struct.unpack)

```swift
let a = unpack(">hBsf", unhexlify("0500 01 41 3fc00000")!)
assert(a[0] as? Int == 1280)
assert(a[1] as? Int == 1)
assert(a[2] as? String == "A")
assert(a[3] as? Double == 1.5)
```

```swift
let f = FileHandle(forReadingAtPath: "/bin/ls")!
let b = unpack("<2H", f.readDataOfLength(4))
f.closeFile()
assert(b[0] as? Int == 64207)
assert(b[1] as? Int == 65261)
```
**caveats**

pack() and unpack() should behave as Python's struct module https://docs.python.org/2/library/struct.html BUT:

- native size and alignment `@` is not supported
- as a consequence, the byte order specifier character is mandatory and must be among `=<>!`
- native byte order `=` assumes a little-endian system (eg. Intel x86)
- Pascal strings `p` and native pointers `P` are not supported

**Byte Order Format**

| Character | Byte Order |
|---|---|
| = | little-endian |
| < | little-endian |
| > | big-endian |
| ! | network (big-endian) |

**Format Characters**

| Format | C Type | Swift Type | Size |
|---|---|---|---|
| x | pad byte | - | 1 |
| c | char | String of length 1 | 1 |
| b | signed char | Int8 | 1 |
| B | unsigned char | UInt8 | 1 |
| ? | _Bool | Bool | 1 |
| h | short | Int16 | 2 |
| H | unsigned short | UInt16 | 2 |
| i | int | Int32 | 4 |
| I | unsigned int | UInt32 | 4 |
| l | long | Int32 | 4 |
| L | unsigned long | UInt32 | 4 |
| q | long long | Int64 | 8 |
| Q | unsigned long long | Int64 | 8 |
| f | float | Float32 | 4 |
| d | double | Float64 | 8 |
| s | char[] | String |   |
