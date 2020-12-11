---
layout: post
title: Misery Around PlatformNotSupportedException
tags: Visual-Studio .NET
permalink: /misery-around-platformnotsupportedexception-1b890c74a08a
excerpt_separator: <!--more-->
---

Honestly speaking, [PlatformNotSupportedException](https://msdn.microsoft.com/en-us/library/windows/desktop/system.platformnotsupportedexception%28v=vs.71%29.aspx) is not something new, as it was initially introduced on .NET Framework 1.x.

Why do we need to talk about it now when .NET Core is the center of innovation? Well, simply due to the facts that it is highly possible to pop into your eyes.

For example, .NET Standard 2.0 adds a lot to the cross platform API surface, but you should notice that not all of them are implemented everywhere. Some platform might throw PlatformNotSupportedException to tell you that "sorry this method does not work here".

It would be rare that a general API like File.ReadAllText throws such exceptions, but if one method does throw it would be inconvenient to handle the case just for some platform. As a result, Microsoft recommends a new pattern for library authors.
<!--more-->

### The New Pattern
Let's start with some code in #SNMP Library,

``` csharp
public sealed class DESPrivacyProvider : IPrivacyProvider
{
    public static bool IsSupported
    {
        get
        {
#if NETSTANDARD1_3
            return false;
#else
            return true;
#endif
        }
    }
    ...
    public static byte[] Encrypt(byte[] unencryptedData, byte[] key, byte[] privacyParameters)
    {
#if NETSTANDARD1_3
        throw new PlatformNotSupportedException();
#else
        ...
#endif
    }
    ...
}
```

OK. See it? The `Encrypt` method throws `PlatformNotSupportedException`, because within all supported platforms (`net452`, `netstandard1.3`, `netcoreapp2.0`, and so on) the underlying [`DES`](https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography.des?view=netcore-2.0) might exist or not.

The new pattern here is that the exception should be thrown when the underlying platform (`netstandard1.3`) fails to provide the necessary API.

> Note that conditional compilation is also utilized here, as it can be the simplest way to detect the underlying platform.

But the library authors cannot force all users of the library to use `try...catch...` statement to handle the exception. Also they should not be forced to check the underlying platform as the authors already checked that.

Therefore, another property `IsSupported` should be added (as the sample code shows), so users easily check the flag before making the call.

Thus, if you are maintaining a cross platform .NET library today, make sure you assert the gaps and follow the `IsSupported` + `PlatformNotSupportedException` pattern.

### The Documentation Issue
Please carefully test compatibility of any underlying API.

In my case, DES luckily is available on almost all major platforms, but unfortunately AES (though available even in .NET Core 1.0) does not support CFB mode, so #SNMP Library has to claim its AES support is broken.

If I don't test it carefully enough, and blindly follow the documentation (which might not be correct in all aspects), it would be a disaster.

I raised [a GitHub issue](https://github.com/dotnet/docs/issues/4014) to inform Microsoft and hope they can do something soon.

### Update (Nov 2019)
Microsoft does create a new tool called [.NET API analyzer](https://docs.microsoft.com/en-us/dotnet/standard/analyzers/api-analyzer) to help identify places where PlatformNotSupportedException might come out.