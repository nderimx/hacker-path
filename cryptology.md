# Cryptology - study of cryptography & cryptoanalysis
## Croptography - process of communicating securely
## Cryptoanalysis - cracking crytographic communication

* Secure Socket Layer (SSL) -> web encryption
###### --> Opinion: DNS encryption should be mainstream

#### Information Theory => Tool in Crypto

#### Unconditional Security - can't be broken / cryptoanalysis is impossible (even w/ infinite compute power)

#### One-Time Pad - Unconditionally Secure, but impractical

#### Quantum Key Distribution
##### BB84 - Quantum Key Distribution Scheme
__Polarization__ => angular momentum ("__Spin__") and orientation in space or __direction__ of a particle (we will use the word spin instead of angular momentum)

###### Entangled Particles
* Will always have the opposite spin if both are measured in the same basis/angle.
* If two particles are measured in different bases/angles, you can't be sure what spin the other has.

###### Spin can be used as a binary digit. Example: up = 1 ; down = 0

###### Intercepting
* if an eavesdropper __doesn't__ know the measurement basis, they won't know the spin
* if an eavesdropper __does__ know the measurement basis, they will know the spin (and whole message / one-time pad), however the receiver woun't receive anything, or a forged entangled particle, which will be a new random

###### Sending a One-Time Pad

Ensures perfect secrecy, because if an eavesdropper intercepts the one-time pad, the receiver will have a different random one-time pad from the sender, and the encrypted test communication will fail. Both parties discard the one-time pads upon failure, and repeat the key exchange until it succeeds (doesn't get jammed).
--
Actually this method doesn't ensure secrecy, because the eavesdopper would know the one-time pad of the receiver and sender respectively, and could also forge protocol handshakes (Man in the Middle Attack). This is true if it's known beforehand what bases whill be used for each bit.

###### BB84 full spec
Both parties __don't__ agree on a measuring basis, but use a random one for each bit of the one-time pad (choosing from two or three bases). After measuring, both compare the bases publically and only keep the ones that match with each other.
This ensures that an eavesdropper never has the entire one-time pad of the sender, because they must also choose the bases randomly right there and then, otherwise the legitimete receiver will measure the spin and collapse the polarization, resulting in the eavesdropper not having anything to measure.
Only downside of an eavesdropper is that they will still jam the key exchange anytime they intercept.

#### Computational Security
* A Cryptosystem is Computationally Secure ?(if) the current best algorithm for breaking it requires unreasonable computation.
* There is currently no way to prove that an encryption-breaking algorithm is the best one.

## Algorithmic Run TIme
Growth rate of the time complexity of an algorithm with respect to input size is more important than the time complexity for any fixed imput. However, this might not hold true for specific real-world applicaitons. In other words, rarely an algorithmic run time like: 2n^2 + 5 is better than 2n + 365

### Asymptotic Notation - Big-O Notation: O(nˆ2)

## Symmetric Encryption
### Block Ciphers:
Blocks of fixed size get XORed by pseudo random blocks of same size
* DES
* Blowfish
* AES

### Stream Ciphers
Stream of pseudo random bits XORs continuous stream of data
* RC4
* LSFR

### Confusion & Diffusion - in Block Ciphers
Confusion: hide relationship between message, ciphertext and key
Diffusion: spread influence of message & key bits over the ciphertext
- Product Ciphers use both (some of them: DES &  AES)

DES uses a Feistel Network

- ensures algorithm is invertible.

Message is divided in two: Left, L & Right, R
`for i<=16:`
    `L[i] = R[i-1]`
    `R[i] = L[i-1] XOR f(R[i-1], K[i])`         for a subkey K and some function f
16 rounds in order to defend against differential cryptoanalysis

DES uses 56 bit keys, which can be bruteforced in weeks
Triple-DES uses 112 bits, which should be safe

### Lov Grover's Quantum Search Algorithm O(n^1/n)

## Asymmetric Encryption

### RSA - needs edit... too much rambling
Asymmetric Encryption & Decryption are inverse operations

There must be a trapdoor one way function

#### One way function: m^e mod N ≡ c; because you would have to brute force m given all the other variables.

#### trap door usage should look something like: c^d mod N ≡ m
To achieve this we must find a valid equation like this: m^ed ≡ m mod N

#### an actual trapdoor: Euclid's Prime Factorization (_O(n log log n)_)

euler's totient funciton (how breakable a number is): φ(X) , outputs the number of integers that __don't__ share a common factor with x (except one / greater than one) (it takes 100s of years for 1024bit numbers _O(n^(1/2))_)

examples (where f(A) finds common factors):
* φ(8)=4, because: __f(8,1)=()__ ; f(8,2)=(2) ; __f(8,3)=()__ ; f(8,4)=(2,4) ; __f(8,5)=()__ ; f(8,6)=(2) ; __f(8,7)=()__ ; f(8,8)=(2,4,8)
* φ(7)=6, because: __f(7,1)=()__ ; __f(7,2)=()__ ; __f(7,3)=()__ ; __f(7,4)=()__ ; __f(7,5)=()__ ; __f(7,6)=()__ ; f(7,7)=(7)

for p is prime, __φ(p)=p-1__ => our _trapdoor__ tool (_O(1)_)
(phi) φ property: __φ(a*b)=φ(a)*φ(b)__
for p1 and p2 are prime and n=p1*p2, __φ(n)=(p1-1)*(p2-1)__

Euler's Theorem: m^φ(n) ≡ 1 mod n

Some Modular Arithmetic Properties:
* If a ≡ b mod(N) , then k*a ≡ k*b mod(N) for any integer k
* If a ≡ b mod(N), then a^k ≡ b^k mod(N) for any integer k

m^φ(n) ≡ 1 mod n // ^k , for any integer k
m^φ(n)*k ≡ 1 mod n // *m
m * m^φ(n)*k ≡ m mod n
m^k*φ(n)+1 ≡ m mod n <<<<<<<<<<<<<< now we have that: m^x ≡ m mod n, where x = k*φ(n)+1
if we "split": x=d*e AND if m^d ≡ c mod n THEN, m^ed ≡ m mod n AND c^e ≡ m mod n ARE equivalent 
or
m^ed ≡ m mod n        }
m^d ≡ c mod n // ^1/d }

m^ed ≡ m mod n   }
m ≡ c^1/d mod n  }

m^ed ≡ c^1/d mod n // ^d
m^edd ≡ c mod n
m^d ≡ c mod n
--

for 1 < e < φ(n) and no common factors higher than 1 between e and φ(n):
security note for e: in devices with limited computing power, a small e is used (3, 5, 35...), which is more insecure. A popular choice is 2^16 + 1 = 65537
d = (kφ(n)+1)/e
m^(kφ(n)+1)/e mod n ≡ c AND c^e ≡ m mod n
m^e ≡ c mod n AND c^(kφ(n)+1)/e mod n ≡ m

pbk = {e, n}
prk = {k, n, p1, e}

pseudocode would be:
`c=pow(m,(k(n/p1-1)(p1-1)+1)/e)%n`
`m=pow(c, e)%n`

## Hybrid Ciphers

Uses asymmetric encryption to distribute a randomly generated key, used for symmetrically encrypted communication, for speed & efficiency.

* SSh
* SSL
* PGP

Usually, attacking the cipher won't work. MitM attack the exchange algorithm then.

## Man-in-the-Middle Attacks

