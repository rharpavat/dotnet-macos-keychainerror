# dotnet-macos-keychainerror
Min-repro example for MacOS error: -25294 w/ dotnet core 2.1/3.1 on macOS 10.14 Mojave and 10.15 Catalina

# Program Details
This program imports the `System.Security.Cryptography` namespace and runs a one-line hello-world program.

# Context
We are running into an issue when importing the `System.Security.Cryptography` library into a .NET Core project on MacOS. Simply running code that imports this library causes several MacOS keychain errors of the format:

```
MacOS error: -25294
2020-03-12 15:41:57.256559-0700 0x4011c    Default     0x0                  54996  0    dotnet: (Security) [com.apple.securityd:security_exception] MacOS error: -25294
2020-03-12 15:41:57.258111-0700 0x4011c    Default     0x0                  54996  0    dotnet: (Security) [com.apple.securityd:security_exception] MacOS error: -25294
2020-03-12 15:41:57.472193-0700 0x40115    Default     0x0                  54996  0    dotnet: (Security) [com.apple.securityd:security_exception] MacOS error: -25294
2020-03-12 15:41:57.476801-0700 0x40115    Default     0x0                  54996  0    dotnet: (Security) [com.apple.securityd:security_exception] MacOS error: -25294
...
```

The error is mentioned on the [Apple developer website][1].

I was able to find a bit more detail [here][2], which states that this error code is:
> Returned by the functions KCUnlock, KCSetDefaultKeychain, KCGetStatus, and KCGetIndKeychain to indicate that the specified keychain was not found.

I was also able to find specific descriptions of these functions [here][3].


[1]: https://developer.apple.com/documentation/coreservices/errkcnosuchkeychain
[2]: http://mirror.informatimago.com/next/developer.apple.com/documentation/Carbon/Reference/Keychain_Manager/keychain_manager/ResultCodes.html#//apple_ref/doc/uid/TP30000053/RCM0142
[3]: http://mirror.macintosharchive.org/gsehi.com/computing/software/macos/Media/PDF/TradMacDoc/Other/1999/Keychain_security.pdf

# How to Run
## Requirements:
* macOS 10.14 Mojave or 10.15 Catalina (I do not have access to a machine running previous version of macOS so cannot confirm it occurs on those versions)
* .NET Core 2.1 or 3.1

## Steps to reproduce:
1. Verify there are no keychain errors in the last minute using the command `log show --last 1m | grep "MacOS error: -25294"`
2. Run the program using `dotnet run Program.cs`.
3. Run the command to show logs again: `log show --last 1m | grep "MacOS error: -25294"`. You should see several keychain errors as formatted above print to console.


