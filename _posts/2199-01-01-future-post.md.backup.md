---
title: 'Research on ECDSA'
date: 2023-04-02
permalink: /posts/2023/08/blog-post-4/
tags:
  - cool posts
  - category1
  - category2
---

## Abstract
In today's era of the internet, data security has become an increasingly important concern. In network communication, encryption technology plays a crucial role in ensuring the confidentiality, integrity, and authenticity of data. One widely used encryption algorithm is ECDSA (Elliptic Curve Digital Signature Algorithm), which is based on the mathematical properties of elliptic curves.

In this article, I will dive into the technical details of ECDSA and explore how it works, its security features, and its applications. I will also discuss the differences between ECDSA and other encryption algorithms, and provide examples of how it is used in real-world scenarios.
## Introduction
ECDSA (Elliptic Curve Digital Signature Algorithm) is a public-key cryptography algorithm based on elliptic curve theory. It is used to verify the authenticity of digital signatures and ensure the integrity and confidentiality of data transmitted over the internet. ECDSA is widely used in many applications, including secure communications, digital certificates, and blockchain technology. The algorithm involves selecting a suitable elliptic curve and a base point, generating a private key and public key pair, and using a digital signature scheme to sign and verify messages. Compared to traditional RSA-based schemes, ECDSA provides equivalent security with shorter key sizes and faster processing speeds, making it a popular choice for secure communications and transactions.


Here first briefly introduce the process of ECDSA:


ECDSA can use multiple elliptic curves, but in practical applications, some recognized standard curves are usually used, such as those recommended by NIST. NIST has recommended multiple elliptic curves, including P-256, P-384, and P-521, which use different sizes of finite field orders and provide different levels of security. P-256 is one of the most commonly used curves, which uses a 256-bit finite field order and provides 128-bit security strength.

NIST is the abbreviation for the National Institute of Standards and Technology, which is a non-regulatory agency of the United States federal government. It is mainly responsible for research and development in science, technology, and measurements, as well as the formulation of related standards and guidelines. In the field of cryptography, NIST is responsible for developing and recommending standard algorithms and curves, such as the elliptic curves used in AES encryption algorithm and ECDSA digital signature algorithm.

In this article, we analyze ECDSA using the P-256 elliptic curve


## Detailed steps of ECDSA
The equation of the elliptic curve used in ECDSA is **y^2 = x^3 + ax + b**, where a and b are constants over a finite field and their specific values depend on the chosen curve. For the P-256 curve, the value of a is **-3** and the value of b is **41058363725152142129326129780047268409114441015993725554835256314039467401291.**

 ![](https://malware.news/uploads/default/original/2X/f/f7507cb1f926e02e63512b84b024ef24f32bfab0.png)

ECDSA is performed on an elliptic curve, and the size of the domain depends on the specific curve selected. For the P-256 curve, the size of the domain is **2^256-2^224+2^192+2^96-1**, which is represented in hexadecimal as **FFFFFFFF 00000001 00000000 00000000 00000000 FFFFFFFF FFFFFFFF**. This value is approximately **1.1579 x 10^77**.

Because the P-256 curve is a special elliptic curve, its parameters are carefully chosen to make addition and multiplication operations on this curve more efficient. Additionally, the curve has some special properties, such as reversibility and symmetry, that make operations on the curve easier to implement. Furthermore, the curve has been extensively studied and widely used, and there are many optimization algorithms available specifically for this curve, which further enhances its processing efficiency.

The P-256 curve is a reversible curve, which means that any point on the curve (except for the point at infinity) can be obtained through certain mathematical operations (such as addition, subtraction, and multiplication) with another point on the curve. Similarly, given two points on the curve, one can compute the discrete logarithm problem to derive the mathematical operation that maps one point to the other. This property is one of the foundations of elliptic curve cryptography (ECC) and a key factor in ensuring the security of the algorithm.

### Base point

The base point of ECDSA (Elliptic Curve Digital Signature Algorithm) is selected based on specific properties of the elliptic curve and generally needs to satisfy the following conditions:

The base point is a fixed point on the elliptic curve and cannot be obtained through addition or subtraction of other points on the curve.
The order of the base point should be a large prime number to ensure that multiples of the base point can cover all points on the elliptic curve.
The base point selection should be public, and all users need to use the same base point.
For the P-256 curve, its base point is predetermined and has been made public. Its coordinates are:

x: 115792089210356248762697446949407573530086143415290314195533631308867097853951

y: 41058363725152142129326129780047268409114441015993725554835256314039467401291

Such a base point selection is determined by cryptographic researchers after a series of testing and analysis, with the goal of ensuring security and efficiency.

### Generator

In mathematics, a generator refers to an element of a group such that every other element of the group can be obtained by repeatedly applying the group's binary operation to the generator.

In cryptography, generators are often used in key exchange and digital signature algorithms to create random numbers and keys. For example, in the Diffie-Hellman key exchange algorithm, selecting a generator and sharing that value is a crucial step in implementing the key exchange.

### Modular Inverse

In mathematics, for a given number a and modulus n, if there exists another number b such that the product of a and b modulo n equals 1, then we call b the modular inverse of a mod n, also known as the reciprocal of a modulo n. The notation is represented as b ≡ a⁻¹ (mod n). If there is no modular inverse of a modulo n, then a is said to be non-invertible modulo n.

##### Generate private key
Randomly select an integer d as the private key, where 1 ≤ d ≤ n-1, and n is the order of the selected elliptic curve, that is, a multiple of the base point G. This integer d should be generated by a random number generator and must be kept secret.
#### Compute public key
Compute the public key Q, which is d times the base point G, that is, Q = dG. Here, multiplication is point multiplication on the elliptic curve. The public key Q is a point on the elliptic curve and can be publicly disclosed
#### Sign message
Assuming the message to be signed is M, first choose a random integer k, where 1 ≤ k ≤ n-1. Then, calculate the coordinates of kG, denoted as (x1, y1). Take x1 mod n, if x1 = 0, then select another random number k. Next, calculate a hash value, such as SHA-256(M), to get a fixed-length message digest.

Then, calculate a value r = x1 mod n. If r = 0, then select another random number k and recalculate r. Next, calculate s = k^(-1) * (hash(M) + d*r) mod n, where k^(-1) is the multiplicative inverse of k mod n.

The signature value (r, s) is the ECDSA signature of the message M.
#### Verify signature
To verify the signature (r, s), you need to know the public key Q and the message M. First, calculate the hash value of the message hash(M). Then, calculate a value w = s^(-1) mod n, where s^(-1) is the multiplicative inverse of s mod n. Next, calculate two points u1 = (hash(M) * w) mod n and u2 = (r * w) mod n. Then calculate the point (x, y) = u1 * G + u2 * Q. If x mod n = r, the signature is valid. Otherwise, the signature is invalid.