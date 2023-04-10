# Cryptography

## RSA

### picoCTF Mind your Ps and Qs

We are given the e (public exponent), n (modulus), and c (ciphertext) of an RSA operation. The modulus is relatively small, so it can just be factored (we might not even have to do the work ourselves, factorDB likely has the correct factorization for us.)

We go over to dcode.fr, plug in our values, and recieve a generated flag.

### picoCTF SRA

This is a RSA challenge we netcat into. It gives us some parameters with names that correspond to sins, and expects a third. It can be inferred it is testing our knowledge of RSA. We are given a source code, and we can enumerate what variables correspond to what.

There's an extremly useful writeup that helped me brush up on general RSA concepts.

https://mregraoncyber.com/picoctf-writeup-rsa-pop-quiz/

It's important to figure out what is what, which is tricky. 

Vainglory is the user input.
Sloth is a the RSA exponent (e)
Gluttony and greed are two primes, P and Q.
Lust the product of Gluttony(P) and Greed(Q), so it is N.
Envy is the totient.
Anger is very likely the ciphertext, since it is calling the public key (e, N)

We are given Anger, and Envy. This is going to be very tricky, since we are effectively only given the ciphertext and the private exponent. We are de jure given the public exponent as well, since it is hardcoded to be 65537.

There's also an extremely useful stackexchange post that outlines the number theory here.

https://crypto.stackexchange.com/questions/105734/crack-rsa-with-e-and-d#comment226368_105734

The theory is this: there exists an equation relating e, d, p, and q.

It looks something like this = ed - 1 = kuv, where u  and v can be used to derive p and q via way of (2u + 1) = p.

So we break condense ed - 1 into some term T. We go to FactorDB, and have it factor (ed-1) for us. From the factors of (ed-1), some combination makes u, some combination makes v, and some combination makes t.

A better way to think of this, is that we choose a subset of (ed-1)'s factors, and that subset will multiply to u, v or k. For our purposes, we only really care about some subset generating u, and some subset generating v.

So, we write a loop that goes through this array, and tests every single possible combination of factors. It starts by choosinga combination of numbers that is the length of the array long, and then a combination that is the length - 1, until it reaches 0 and has gone through every single possible combination. 

This is an intensive process. Our loops runs a number of times equal to the geometric series of (L Choose N) + (L Choose (N-1)) ... (L Choose 0). 

It takes this combination of factors that has been chosen, and multiplies it. This gives us a candidate for u or v. We know from the script that 128 bit primes are used, so we can expect the length of u and v  to be somewhere around 124-126 bits. We estimate this by taking the log2 of the number, which gives us its estimated length in bits. If the length is not in the range of 124-126, we simply go to the next iteration.

If we have a valid u or v, we pass it to the next step. We transform it by way of (2u-1) = p. If the resultant p is prime, and if it is between 127-129 bits long, it could've been a prime that was used in the encryption process. We store it in an array.

Eventually, the loop finishes, and we have a list of canidate primes. Of this list, one of the primes is p, and one of them is q. By extension, somewhere in that list is a combination of two primes were used during the key generation process.

So, we use a process similar to the last one. We take the pool of primes, and iterate through every possible combination of two numbers of the array. It follows that one of these combinations MUST be p and q.

To check if we're correct, we can simply re-do the private key generation process. We were given d (private exponent) in the original equation. If our p and q generate the same d that we were given, we know that our combination for p and q must be correct. If this is the case, we use p and q to generate n, and decrypt with c, d, and n.

This gives us the plaintext, which we enter into the nc connection, and we will be greeted with the flag.

## Other

## picoCTF 13

Name implies rot13 cipher. Throw it into cyberchef, and get the flag.

## picoCTF Caesar

Name implies caesar cipher (which is just rot13 with a custom offset). Going to cyberchef, plugging ciphertext into rot13, and playing with the offset gets us the flag.

## picoCTF vigenere

Name implies vigenere cipher (caesar cipher, but there's a key. a key of abc means that the first letter is offset by a, the second by b, the third by c, the fourth by a, and this repeats).

Going to cyberchef, and plugging in the appropriate settings (vigenere decode with key) gives us the flag.
### picoCTF The Numbers

We are given a png, with some numbers, a curly brace, more numbers, than a closing curly brace, like so: ```15 20 { 10 15 22 22}```

We plug the numbers into cyberchef, and it auto detects that a1z26 encryption is being used, which just maps letters to numbers mod 26

### picoCTF Mod 26

We know it's a rotation cipher (it looks like one, and the name is mod26 and the description asks if we know what rot13 is)
Go to cyberchef, throw it into cyberchef, and it's rot13 decoding to get us the flag.

### picoCTF ReadMyCert

We download and run file, and find that it is a PEM certificate request file. We cat it, and it looks encoded. We google "Pem Certficate Request Cipher Decoder", and get to the following link.

https://certlogik.com/decoder/

### picoCTF Rotation

We download and cat the file. It is very obviously an encrypted flag, and the first 7 letters are 'picoCTF'. We plug it into cyberchef, and cyberchef doesn't automatically decode it. We give cyberchef the "rot13" option, and play around with the offset. We find that rot13 with a -8 offset decrypts the flag.

## picoCTF HideToSee

```stegseek atbash.jpg /usr/share/wordlists/rockyou.txt```

This one was tricky. Had to go through a lot of tools, and eventually stegseek worked. It's a very interesting tool - it went through all of rockyou.txt in a few seconds. Stegseek gave us the output of some sort of encrypted text. Based on the challenge name, it's an atbash cipher. Decod.fr has an atbash decoder to help us.