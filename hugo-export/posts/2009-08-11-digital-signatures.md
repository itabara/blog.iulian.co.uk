---
title: Digital Signatures
author: Iulian
type: post
date: 2009-08-11T20:43:00+00:00
url: /2009/08/digital-signatures/
categories:
  - Cryptography
tags:
  - RSACryptoServiceProvider
  - RSAPKCS1SignatureFormatter

---
#### Introduction

<p align="justify">
  Digital signatures are used to verify identity of the sender and ensure data integrity. They are often used along with public key encryption.
</p>

#### How Digital Signature work

In <a href="http://www.iuliantabara.com/2008/02/cryptography-and-net-framework-introduction/" target="_blank">Part 1</a> we mentioned how digital signatures work.

  1. <div align="justify">
      Sender applies hash algorithm to the data being sent and creates a message digest. Message digest is compact representation of the data being sent.
    </div>

  2. <div align="justify">
      Sender then encrypts the message digest with the private key to get a digital signature
    </div>

  3. <div align="justify">
      Sender sends the data over a secure channel
    </div>

  4. <div align="justify">
      Receiver receives the data and decrypts the digital signature using public key and retrieves the message digest
    </div>

  5. <div align="justify">
      Receiver applies the same hash algorithm as the sender to the data and creates a new message digest
    </div>

  6. <div align="justify">
      If sender’s digest and receiver’s digest match then it means that the message really came from the said sender.
    </div>

#### Related classes

<p align="justify">
  .NET Framework provides classes <strong>RSACryptoServiceProvider</strong>, <strong>RSAPKCS1SignatureFormatter</strong> and <strong>RSAPKCS1SignatureDeformatter</strong> that allow you create and verify digital signatures. All of them reside in System.Security.Cryptography namespace.
</p>

##### Example

<p align="justify">
  In this example we will be creating a class called <strong>DigitalSignatureHelper</strong> that allows us to generate digital signatures and verify signatures. Note in order to run this example you need <strong>MD5HashHelper</strong> that we developed in the previous part
</p>

**DigitalSignatureHelper.cs**

<pre class="lang:c# decode:true ">public class DigitalSignatureHelper
{
    RSAParameters m_private;
    RSAParameters m_public;
 
    public byte[] CreateSignature(byte[] hash)
    {
        RSACryptoServiceProvider RSA = new RSACryptoServiceProvider();
        RSAPKCS1SignatureFormatter RSAFormatter = new RSAPKCS1SignatureFormatter(RSA);
        RSAFormatter.SetHashAlgorithm("MD5");
        m_public = RSA.ExportParameters(false);
        m_private = RSA.ExportParameters(true);
        return RSAFormatter.CreateSignature(hash);
    }
 
    public bool VerifySignature(byte[] hash, byte[] signedhash)
    {
        RSACryptoServiceProvider RSA = new RSACryptoServiceProvider();
        RSAParameters RSAKeyInfo = new RSAParameters();
        RSAKeyInfo.Modulus = m_public.Modulus;
        RSAKeyInfo.Exponent = m_public.Exponent;
        RSA.ImportParameters(RSAKeyInfo);
        RSAPKCS1SignatureDeformatter RSADeformatter = new RSAPKCS1SignatureDeformatter(RSA);
        RSADeformatter.SetHashAlgorithm("MD5");
        return RSADeformatter.VerifySignature(hash, signedhash);
    }
}</pre>

Let’s understand the code step-by-step.

  1. We create a class called **DigitalSignatureHelper** with two private variables and two methods.
  2. The class level variables m\_private and m\_public are of type RSAParameters and are used to store public and private key information.
  3. The method CreateSignature() accepts the hash value that has to be signed and returns the digitally signed hash as a return value
  4. Inside this function we create an instance of a class called RSACryptoServiceProvider
  5. We also create an instance of a class called RSAPKCS1SignatureFormatter and pass the instance of RSACryptoServiceProvider in its constructor.
  6. The RSAPKCS1SignatureFormatter class is used to create PKCS #1 (Public Key Cryptographic Signature) version 1.5 signature. Where as RSACryptoServiceProvider provides encryption services.
  7. Since we will be using MD5 as a hashing algorithm, we call SetHashAlgorithm()method of RSAPKCS1SignatureFormatter and pass &#8220;MD5&#8221; as a parameter. If your hashing algorithm is SHA1 you would have passed SHA1 instead.
  8. Then we call ExportParameters() method of RSACryptoServiceProvider to get public and private keys generated. We store these keys the class level variables m\_public and m\_private respectively.  
    Finally we call CreateSignature() method of RSAPKCS1SignatureFormatterclass which returns the signature. The same is returned as the return value of the function.
  9. The VerifySignature() method accepts two parameters – original hash value and signed hash value. It compares the hashes and return true if they match.
 10. Inside this function we create an instance of RSACryptoServiceProvider class.
 11. We need to supply key information during signature verification and hence we create an instance of RSAParameters structure.
 12. The Modulus and Exponent properties of this structure are set to the equivalent properties of previously obtained public key (m_public).
 13. We then call ImportParameters() method of RSACryptoServiceProvider to import the key information into the instance.
 14. Then we create an instance of RSAPKCS1SignatureDeformatter class. This class is used to verify RSA PKCS #1 version 1.5 signatures.
 15. Again, we set the hashing algorithm to MD5 using SetHashAlgorithm() method of RSAPKCS1SignatureDeformatter class.
 16. Finally we call VerifySignature() method of RSAPKCS1SignatureDeformatterclass and pass original hash value and signed hash value to it. This method returns true if the signature is verified successfully else it returns false. The same return value is returned as to the caller.

#### Summary



In this article we learnt about digital signatures. Digital signatures allow you to verify that the data came from known sender. The classes **RSACryptoServiceProvider**, **RSAPKCS1SignatureFormatter** and **RSAPKCS1SignatureDeformatter** from **System.Security.Cryptography** allow you to work with digital signatures.

Check <a href="http://www.dotnetbips.com" target="_blank">http://www.dotnetbips.com</a> for original articles.