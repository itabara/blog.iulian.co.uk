---
title: Secret Key Encryption
author: Iulian
type: post
date: 2008-02-28T19:47:00+00:00
url: /2008/02/secret-key-encryption/
categories:
  - Cryptography
tags:
  - Triple-DES

---
#### Triple-DES

<p align="justify">
  The <strong>System.Security.Cryptography</strong> namespace contains a class called <strong>TripleDESCryptoServiceProvider</strong> that provides Triple-DES encryption to your data. DES stands for Data Encryption Standard and the word triple is used because it encrypts the original data thrice.
</p>

The secret key encryption needs two things to encrypt the data:

  * A secret key 
      * <div align="justify">
          An initialization vector
        </div></ul> 
    
    <p align="justify">
      The encryption algorithms employ use a chaining technique to encrypt the data. In this technique the entire data to be encrypted is divided in smaller blocks. The previously encrypted block of data is used to encrypt the current one and the process repeats.
    </p>
    
    <p align="justify">
      The <strong>Initialization Vector</strong> (IV) serves as a seed that is used to encrypt and decrypt the first block of bytes. This ensures that no two blocks of data produce the same block of encrypted text.
    </p>
    
    <p align="justify">
      For using <strong>TripleDESCryptoServiceProvider</strong> the encryption key must be of 24 bytes and the initialization vector must be of 8 bytes.
    </p>
    
    Example of using **TripleDESCryptoServiceProvider** class:
    
    **SecurityHelper.cs**
    
    <pre class="lang:c# decode:true ">using System;
using System.IO;
using System.Security.Cryptography;
using System.Text;
 
namespace CryptographySamples
{
    public class SecurityHelper
    {
        private byte[] Key; // secret key
        private byte[] IV; // initialization vector
 
        public byte[] Encrypt(String strData)
        {
            byte[] data = ASCIIEncoding.ASCII.GetBytes(strData);
            TripleDESCryptoServiceProvider tdes = new TripleDESCryptoServiceProvider();
            if (Key == null)
            {
                tdes.GenerateKey();
                tdes.GenerateIV();
                this.Key = tdes.Key;
                this.IV = tdes.IV;
            }
            else
            {
                tdes.Key = this.Key;
                tdes.IV = this.IV;
            }
 
            ICryptoTransform encryptor = tdes.CreateEncryptor();
            MemoryStream ms = new MemoryStream();
            CryptoStream cs = new CryptoStream(ms, encryptor, CryptoStreamMode.Write);
            cs.Write(data, 0, data.Length);
            cs.FlushFinalBlock();
            ms.Position = 0;
            byte[] result = new byte[ms.Length];
            ms.Read(result, 0, (int)ms.Length);
            cs.Close();
            return result;
        }
 
        public string Decrypt(byte[] data)
        {
            TripleDESCryptoServiceProvider tdes = new TripleDESCryptoServiceProvider();
            tdes.Key = this.Key;
            tdes.IV = this.IV;
 
            ICryptoTransform decryptor = tdes.CreateDecryptor();
            MemoryStream ms = new MemoryStream();
            CryptoStream cs = new CryptoStream(ms, decryptor, CryptoStreamMode.Write);
            cs.Write(data, 0, data.Length);
            cs.FlushFinalBlock();
            ms.Position = 0;
            byte[] result = new byte[ms.Length];
            ms.Read(result, 0, (int)ms.Length);
            cs.Close();
            return ASCIIEncoding.ASCII.GetString(result);
        }
    }
}</pre>
    
    Check [http://www.dotnetbips.com][1] for original articles.

 [1]: http://www.dotnetbips.com/