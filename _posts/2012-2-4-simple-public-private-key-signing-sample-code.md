---
layout: post
title: Simple Public/Private Key Signing Sample Code
tags: IIS
permalink: /simple-public-private-key-signing-sample-code-6f95d19fdbc
excerpt_separator: <!--more-->
---

I want to sign a file and generate a signature for it. Then next time I can verify the signature to see if this file has been modified. How can I achieve this goal in C# using public/private keys? I searched but found no good guide on it. Therefore, I think I may write a simple sample for beginners to start with.
<!--more-->

### Prepare the Keys
It is not safe to keep the keys in simple text file, so here we use a CER file to store the public key, and a PFX file to store both (private+public). There are other common file formats for key storage, but loading them into C#/.NET will be problematic.

As this is a demo, I use `makecert `to create the keys, (at Visual Studio command prompt)
``` bash
makecert.exe -sv MyKey.pvk -n "CN=LeXtudio" MyKey.cer
```

This command will ask you for a password to protect the private key. After executing, you will have a private key file `MyKey.pvk` and a public key file `MyKey.cer` available. Now execute this to create the PFX file,
``` bash
pvk2pfx.exe -pvk MyKey.pvk -spc MyKey.cer -pfx MyPFX.pfx -po test
```

This command will ask you for the password you set above to decrypt the private key. Then it will use the new password you provided after `-po` (here I use "test") to protect the PFX file.

At this moment, we have the CER file which contains the public key, and the PFX file which contains both keys.

> Note that Windows SDK 8 shipped with Visual Studio 11 Developer Preview may not be able to generate the keys correctly.

### Sign and Verify the Data
Now we can sign and verify the data using the following code,
``` csharp
private static byte[] Sign(byte[] data, X509Certificate2 privateKey)
{
  if (data == null)
  {
    throw new ArgumentNullException("data");
  }
  if (privateKey == null)
  {
    throw new ArgumentNullException("privateKey");
  }
  if (!privateKey.HasPrivateKey)
  {
    throw new ArgumentException("invalid certificate", "privateKey");
  }
  var provider = (RSACryptoServiceProvider)privateKey.PrivateKey;
  return provider.SignData(data, new SHA1CryptoServiceProvider());
}

private static bool Verify(byte[] data, X509Certificate2 publicKey, byte[] signature)
{
  if (data == null)
  {
    throw new ArgumentNullException("data");
  }
  if (publicKey == null)
  {
    throw new ArgumentNullException("publicKey");
  }
  if (signature == null)
  {
    throw new ArgumentNullException("signature");
  }
  var provider = (RSACryptoServiceProvider)publicKey.PublicKey.Key;
  return provider.VerifyData(data, new SHA1CryptoServiceProvider(), signature);
}
```

### Load the Keys
``` csharp
private static X509Certificate2 LoadPrivateKey()
{
  return new X509Certificate2(@"d:\temp\test\MyPFX.pfx", "test");
}
private static X509Certificate2 LoadPublicKey()
{
  return new X509Certificate2(@"d:\temp\test\MyKey.cer");
}
```

### Final Test
``` csharp
var test = new byte[] {0x84, 0x73};
var sig = Sign(test, LoadPrivateKey());
Console.WriteLine(Verify(test, LoadPublicKey(), sig));
```

### Notes
You may explore the following fields,

* How should the PFX file be deployed? Typically it should be installed to certificate store. Then how to query the keys from certificate store in C#/.NET?
* How is the signature generated in the sample? Can you use other algorithm to generate the hash instead of SHA-1?
* Can you use other methods of `RSACryptoServiceProvider` instead of `SignData` and `VerifyData`?

### References
* http://blogs.msdn.com/b/maximelamure/archive/2007/01/24/create-your-own-pfx-file-for-clickonce.aspx
* http://www.fryan0911.com/2009/04/c-how-to-sign-and-verify-digital.html
