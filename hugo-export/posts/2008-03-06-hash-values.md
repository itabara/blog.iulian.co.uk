---
title: Hash Values
author: Iulian
type: post
date: 2008-03-06T20:17:00+00:00
url: /2008/03/hash-values/
categories:
  - Cryptography
tags:
  - MACTripleDES
  - MD5CryptoServiceProvider
  - SHA1Managed

---
<p align="justify">
  In this part we are going to learn how to ensure that data coming to you has not been tampered with during the transfer. The technique that we will be using is hash. Hash values allow us to verify the integrity of data. The hash value of received data can be compared to the hash value of data that was sent to check if the data is tampered.
</p>

#### .NET Framework classes for creating hashes

.NET Framework provides following main classes to work with hashes:

  * SHA1Managed
  * MD5CryptoServiceProvider
  * MACTripleDES

<p align="justify">
  Since SHA1 is now a broken algorithm, we will use <strong>MD5CryptoServiceProvider</strong> to generate hash values.
</p>

<p align="justify">
  <strong>Example</strong><br />We are going to create a helper class that will help us create and verify hash values using MD5 algorithm. The class contains two methods – GetHash() and VerifyHash(). The former accepts string whose hash value is to be generated and returns the computed hash as a byte array. The later accepts the message as it was received and the hash generated previously and returns true if the message is not altered during transmit otherwise returns false.
</p>

**MD5HashHelper.cs**

<pre class="lang:c# decode:true ">public class MD5HashHelper
{
    public byte[] GetHash(string message)
    {
        byte[] data;
        data = System.Text.UTF8Encoding.ASCII.GetBytes(message);
        MD5CryptoServiceProvider md5 = new MD5CryptoServiceProvider();
        return md5.ComputeHash(data, 0, data.Length);
    }
 
    public bool VerifyHash(string message, byte[] hash)
    {
        byte[] data;
        data = System.Text.UTF8Encoding.ASCII.GetBytes(message);
        MD5CryptoServiceProvider md5 = new MD5CryptoServiceProvider();
        byte[] hashtemp = md5.ComputeHash(data, 0, data.Length);
 
        for (int x = 0; x &lt; hash.Length; x++)
        {
            if (hash[x] != hashtemp[x])
            {
                return false;
            }
        }
        return true;
    }
}</pre>

Let’s dissect the code step by step:

  1. <div align="justify">
      We first need to import <strong>System.Security.Cryptography</strong> namespace in your class
    </div>

  2. <div align="justify">
      The GetHash() accepts string whose hash value is to be generated and returns the computed hash as a byte array.
    </div>

  3. <div align="justify">
      Inside the function we used UTF8Encoding class and get a byte representation of the string to be transferred.
    </div>

  4. <div align="justify">
      We then create an instance of MD5CryptoServiceProvider class and call it’s ComputeHash by passing the byte created above to it.
    </div>

  5. <div align="justify">
      The ComputeHash() function generates the hash for the given data and returns another byte array that represents the hash value of the data.
    </div>

  6. <div align="justify">
      The VerifyHash() function accepts the message as it was received and the hash generated previously and returns true if the message is not altered during transmit otherwise returns false.
    </div>

  7. <div align="justify">
      Inside this function we again use UTF8Encoding class and generate byte representation of the received message.
    </div>

  8. <div align="justify">
      We then compute hash for this data using the same ComputeHash() method of MD5CryptoServiceProvider class.
    </div>

  9. <div align="justify">
      Finally, we run a for loop and check each and every byte of original hash value and the hash we generated above. If both the hash values are matching we can conclude that the data is not tampered.
    </div>

Check <a href="http://www.dotnetbips.com" target="_blank">http://www.dotnetbips.com</a> for original articles.