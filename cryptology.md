# Cryptology - study of cryptography & cryptoanalysis
## Croptography - process of communicating securely
## Cryptoanalysis - cracking crytographic communication

* Secure Socket Layer (SSL) -> web encryption
######## --> Suggestion: DNS encryption should be mainstream

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

### Asymptotic Notation
