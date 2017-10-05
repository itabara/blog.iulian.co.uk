---
title: Cryptography and .NET Framework Introduction
author: Iulian
type: post
date: 2008-02-26T17:54:00+00:00
url: /2008/02/cryptography-and-net-framework-introduction/
categories:
  - Cryptography
tags:
  - Cryptography

---
<p align="justify">
  Security is key consideration for many applications. Providing authentication and authorization services to your application is just one part of the overall security. What about the data that is being used and transferred in the application? That is where cryptography comes into picture. Cryptography is a huge topic by itself.
</p>

<p align="justify">
  Many times application provide security features such as login forms and role based security. However, what if someone intercepts the data that is being flown over the network? What if someone plays with the data that is being transmitted over the network? What if someone opens SQL Server database that is storing passwords? Cryptography provides solution to such questions. Using .NET Cryptographic classes you can encrypt the data that is being flown in your system or network and then decrypt when you want authenticated user to modify or read it. In short Cryptography provides following features:
</p>

  * Protect data being transferred from reading by third parties 
      * Protect data being transferred from any modification 
          * Make sure that data is arriving from the intended location </ul> 
        #### Types of Cryptographic classes
        
        The overall Cryptographic classes available in .NET framework can be classified in four categories:
        
          * Classes that deal with secret key encryption (also called as Symmetric Cryptography) 
              * Classes that deal with public key encryption (also called Asymmetric Cryptography) 
                  * Classes that deal with digital signatures (also called cryptographic signatures) 
                      * Classes that deal with cryptographic hashes </ul> 
                    All the cryptography related classes can be found in **System.Security.Cryptography **namespace.
                    
                    #### Secret Key Encryption
                    
                    <p align="justify">
                      In Secret Key Cryptography the data being protected is encrypted using a single secret key. This key is known only to sender and receiver. The sender encrypts the data using the secret key. The receiver decrypts the data using the same secret key. It is very important to keep the key secret otherwise anybody having the key can decrypt the data.
                    </p>
                    
                    .NET Framework provides following classes to work with Secret Key Cryptography:
                    
                      * ##### DESCryptoServiceProvider
                        
                          * ##### RC2CryptoServiceProvider
                            
                              * ##### RijndaelManaged
                                
                                  * ##### TripleDESCryptoServiceProvider</ul> 
                                
                                #### Public Key Encryption
                                
                                <p align="justify">
                                  Unlike secret key encryption, public key encryption uses two keys. One is called public key and the other is called as private key. The public key is not kept secret at all where as private key is kept confidential by the owner of that key. The data encrypted by private key can be decrypted only using its corresponding public key and data encrypted using public key can be decrypted using its private key. Naturally, in order to encrypt the data being transmitted you need to use public key. This data can be decrypted only with the corresponding private key.
                                </p>
                                
                                .NET Framework provides following classes to work with public key encryption:
                                
                                  * **DSACryptoServiceProvider** 
                                      * **RSACryptoServiceProvider** </ul> 
                                    #### Digital Signatures
                                    
                                    <p align="justify">
                                      Digital signatures are used to verify identity of the sender and ensure data integrity. They are often used along with public key encryption. Digital signature work as follows:
                                    </p>
                                    
                                      1. <div align="justify">
                                          Sender applies hash algorithm to the data being sent and creates a message digest. Message digest is compact representation of the data being sent.
                                        </div>
                                        
                                          * <div align="justify">
                                              Sender then encrypts the message digest with the private key to get a digital signature
                                            </div>
                                            
                                              * <div align="justify">
                                                  Sender sends the data over a secure channel
                                                </div>
                                                
                                                  * <div align="justify">
                                                      Receiver receives the data and decrypts the digital signature using public key and retrieves the message digest
                                                    </div>
                                                    
                                                      * <div align="justify">
                                                          Receiver applies the same hash algorithm as the sender to the data and creates a new message digest
                                                        </div>
                                                        
                                                          * <div align="justify">
                                                              If sender’s digest and receiver’s digest match then it means that the message really came from the said sender.
                                                            </div></ol> 
                                                        
                                                        The classes **DSACryptoServiceProvider** and **RSACryptoServiceProvider** are used to create digital signatures.
                                                        
                                                        #### Hashes
                                                        
                                                        <p align="justify">
                                                          Hash algorithms create a fixed length output for a given variable length data. If somebody changes the original data even slightly then the hash generated will be different than original hash. They are often used with digital signatures.
                                                        </p>
                                                        
                                                        Some of the classes in .NET that deal with hashes are:
                                                        
                                                          * **SHA1Managed** 
                                                              * **MD5CryptoServiceProvider** 
                                                                  * **MACTripleDES** </ul> 
                                                                #### Random Number Generators
                                                                
                                                                <p align="justify">
                                                                  While working with cryptography classes many times you need to generate cryptographic keys. Random number generators are used for this purpose. .NET provides a class called <strong>RNGCryptoServiceProvider</strong> to generate such random numbers.
                                                                </p>
                                                                
                                                                Below you will find additional details and code samples for different cryptography techniques.
                                                                
                                                                Check [http://www.dotnetbips.com][1] for original articles.

 [1]: http://www.dotnetbips.com/