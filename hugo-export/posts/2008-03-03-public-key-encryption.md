---
title: Public Key Encryption
author: Iulian
type: post
date: 2008-03-03T20:06:00+00:00
url: /2008/03/public-key-encryption/
categories:
  - Cryptography
tags:
  - Public Key Encryption
  - RSACryptoServiceProvider

---
#### Introduction

Data encrypted by public key can be decrypted only by the corresponding private key and vice a versa. One of the most popular algorithm for encrypting and decrypting data using this technique is **RSA**. The acronym RSA stands for _Rivest, Shamir, and Adelman_ who are the inventors of the technique. The .NET framework provides a class called **RSACryptoServiceProvider** that encapsulates this algorithm. In this article we are going to learn how to use this class to secure your data. 

#### Developing a class for encryption and decryption



Many developers don’t want to go into the internals of Cryptography. They simply need a quick and easy way to secure their data. So we are going to develop such reusable class that will do the job of encrypting and decrypting for us.

<p align="justify">
  We will create a class called <strong>PublicKeySecurityHelper</strong> which will have two methods – <strong>Encrypt</strong> and <strong>Decrypt</strong>. In addition we will also create a helper class called <strong>MyRSAInfo</strong>. This class will simply store certain pieces of data (such as public key and private key).
</p>

Here, is the complete code of the class.

**PublicKeySecurityHelper.cs**

<pre class="lang:c# decode:true ">using System;
using System.Security.Cryptography;
using System.Text;
 
namespace CryptoTestRSA
{
    public class PublicKeySecurityHelper
    {
        public RSAInfo Encrypt(String inputData)
        {
            RSAInfo myRSA = new RSAInfo();
 
            CspParameters p = new CspParameters();
            p.Flags = CspProviderFlags.UseMachineKeyStore;
            myRSA.Parameters = p;
 
            RSACryptoServiceProvider rsa = new RSACryptoServiceProvider(p);
            byte[] data = rsa.Encrypt(Encoding.Unicode.GetBytes(inputData), false);
            myRSA.PublicKey = rsa.ToXmlString(false);
            myRSA.PrivateKey = rsa.ToXmlString(true);
            myRSA.Data = data;
 
            return myRSA;
        }
 
        public string Decrypt(RSAInfo encryptedData)
        {
            RSACryptoServiceProvider rsa = new RSACryptoServiceProvider(encryptedData.Parameters);
            rsa.FromXmlString(encryptedData.PrivateKey);
            byte[] data = rsa.Decrypt(encryptedData.Data, false);
            string result = Encoding.Unicode.GetString(data, 0, data.Length);
            return result;
        }
    }
}</pre>

**RSAInfo.cs**

<pre class="lang:c# decode:true ">using System;
using System.Security.Cryptography;
 
namespace CryptoTestRSA
{
    public class RSAInfo
    {
        private string publicKey;
        private string privateKey;
        private CspParameters parameters;
        private byte[] data;
 
        public string PublicKey
        {
            get { return publicKey; }
            set { publicKey = value; }
        }
 
        public string PrivateKey
        {
            get { return privateKey; }
            set { privateKey = value; }
        }
 
        public CspParameters Parameters
        {
            get { return parameters; }
            set { parameters = value; }
        }
 
        public byte[] Data
        {
            get { return data; }
            set { data = value; }
        }
    }
}</pre>

**Program.cs**

<pre class="lang:c# decode:true ">using System;
 
namespace CryptoTestRSA
{
    class Program
    {
        static void Main(string[] args)
        {
            string data = "This is the text to encrypt!";
            PublicKeySecurityHelper helper = new PublicKeySecurityHelper();
            RSAInfo encryptedData = helper.Encrypt(data);
            Console.WriteLine("Original text: {0}", data);
            string result = helper.Decrypt(encryptedData);
            Console.WriteLine("Result: {0}", result);
        }
    }
}</pre>

Below the code step by step:

#### Encrypting data

  1. First we import the required namespaces. Especially System.Security.Cryptography is important one because it contains our core class RSACryptoServiceProvider.
  2. We create a method called Encrypt() that accepts the string to be encrypted and returns an instance of a class called RSAInfo.
  3. RSAInfo is our custom class defined at the bottom of the code. It consists of four public members – PublicKey, PrivateKey, Parameters and Data.
  4. The PublicKey and PrivateKey members store the generated public key and private key respectively.
  5. The Parameters variable is of type CspParameters. This is used to automatically generate public and private keys and reuse them later on.
  6. The Data is an array of bytes and stores the encrypted version of the data
  7. Inside the Encrypt() method we create an instance of CspParameters class and set its Flag property to CspProviderFlags.UseMachineKeyStore. This enumerated value specifies from where the key information should be picked up i.e. from default key container or from machine level key store.
  8. Then we create new instance of RSACryptoServiceProvider class passing the CspParameters instance.
  9. We then call Encrypt() method of RSACryptoServiceProvider class and pass data to be encrypted. Since this parameter is byte array we convert our string into byte array using GetBytes() method. The second parameter of the method indicates whether to use OAEP padding (true) or PKCS#1 v1.5 padding (false). The former can be used only on Windows XP machines and hence we pass False. The Encrypt() method of RSACryptoServiceProvider class returns a byte array that contains encrypted version of the data.
 10. Finally, we fill all the members of RSAInfo class and return to the caller. Note how we call ToXmlString() method first passing False and then passing True to get public and private keys respectively.



#### Decrypting data

  1. In order to decrypt the data we create a method called Decrypt() that accepts an instance of RSAInfo class. This instance must be the one returned by the Encrypt() method explained earlier.
  2. Inside Decrypt() method we create an instance of RSACryptoServiceProviderclass again passing the same CspParameters.
  3. We then call FromXmlString() method of the RSACryptoServiceProvider class and pass the private key generated before. More details [here][1].
  4. Finally, we call Decrypt() method of RSACryptoServiceProvider class and pass the encrypted data. The second parameter of Decrypt method has the same significance as that of the corresponding parameter of Encrypt() method

#### Summary



Public key encryption is a secure way to transfer data over networks. The fact that the private key is not sent in unsafe manner makes it more secure and robust. This technique is used in Secure Socket Layer (SSL) or HTTPS based web sites. The .NET framework class RSACryptoServiceProvider allows you to generate public and private keys, encrypt and decrypt data.

Check [http://www.dotnetbips.com][2] for original articles.

 [1]: http://en.wikipedia.org/wiki/RSA
 [2]: http://www.dotnetbips.com/