# CO685 Term Project: A flexible Java implementation of Merkle Tree hash-based signatures

By William Justin Toth

## Motivation
It is conjectured that within the next decade quantum computers of non-trivial scale could become a reality. If this were to happen then cryptography would be faced with a difficult position, for many of the hardest assumptions upon which the security of the internet's most ubiquitous cryptosystems rest are broken by quantum algorithms. For instance quantum computers are known to be able to take discrete logarithms quickly, and this can be used to break the common digital signature scheme ECDSA.

Fortunately all is not lost for those of us interested in maintaining secrecy with our classical computers in a post-quantum world. Many cryptosystems have emerged since quantum computing rose to popularity that are believed to be secure against attacks from quantum computers. This project in particular concerns itself with one such cryptosystem, known as Merkle Trees, and their application as a digital signature scheme secure against quantum attacks.

## A High-Level Overview of Merkle Trees
The reason Merkle Tree Signatures are believed to be secure against attacks carried out by quantum computers is that their security rests on a vary simple assumption. That assumption is that you have access to a collision-resistant random oracle. In practical terms that is to say you have a hash function (say a SHA variant for instance). At this time it is not known (or believed to be possible for that matter) how a quantum computer could efficiently find hash function collisions, so this assumption implies security in a post-quantum situation.

A Merkle Tree is a complete binary tree where each node has a value corresponding to the hash of the concatenation of its two childrens hashes. The base case, the leaves, have the hashes of some given verification keys to be used with a separate one time signature scheme as their values. The public key of the signature scheme is the hash value at the root of the tree. The remainder of the tree is kept secret until you sign a message. When signing a message you choose a signing key corresponding to leaf, sign using the one time signature scheme under that key and provide that to the verifier. You also give them a set of hash values from nodes, one from each level of the tree, such that the verifier can use these nodes to compute the hashes on the path up from the chosen leaf to the root. They verifier first performs verification of the message under the one time signatures scheme, then computes the aformentioned path up to the Merkle Tree Root and verifies that the hash value they computed matches the hash value of the public key.

Notice that you can sign one message per leaf, so in a sense the scheme is parameterized by how many messages you want to sign. There is a trade off here though. There are exponentially many nodes in the tree as a function of the number of leaves. Clearly it is inefficient to store all these hashes, so schemes have been developed to efficiently traverse the tree finding the hash nodes you need. Some common ones can be found in the source code.

## The Contribution
This project provides a Java implementation of the Merkle Signature Scheme. The main point of interest in this implementation is it was designed with flexibility of use in mind. The specification of the Merkle Digital Signature Scheme leaves many choices open to the user. Those include things like how you wish to sign leaves, what hash function you want to use, and how you want to traverse the tree. The design of this implementation uses a common software design principle sometimes called Coding to Interfaces, where each piece of the implementation refers to interfaces instead of other concrete classes. The advantage of this is that all the different pieces can be swapped out at the users leisure to suit their needs.

We provide in the source two options for One Time Signature Schemes, the methods of Lamport and Winternitz. For Tree Traversal we provide both the Classical method of Merkle in a serial and parallel variation, as well as the faster Logarithmic method that came later. For hash functions we provide Sha512, but it is very simple to implement your favourite one if you want something different. In fact that is really where the power of this imlementation comes in. It should be quite clear from reading the source documentation how a user could write their own leaf signing oracle, or write a wrapper around their hash function of choice and just plug it into this scheme. To borrow another phrase from software design, the pieces of this scheme are loosely coupled and thus very suitable for having components swapped out. Another reason this is a good design choice is that the practical considerations of how to build Merkle Tree schemes have only come under consideration very recently, within the last decade or so, in coordination with interest in post-quantum cryptography, and so as new methods in these areas are developed it should be easy to code up an implemenation of them and plug it into the existing system implemented here.

## Building and Usage

For usage of this software one can see the Test classes or Main method for examples, and read the documenting comments on each class. To build the software one needs JRE 7 and the application Maven. Navigate your command line to the directly containg pom.xml and run "mvn install" from there to build.

## References
1. R. Merkle. Secrecy, authentication, and public key systems. Ph.D. dissertation, Dept. of Electrical Engineering, Stanford Univ., 1979.
2. Georg Becker. Merkle Signature Schemes, Merkle Trees and Their Cryptanalysis. Seminar 'Post Quantum Cryptology'. Ruhr-Universit¨at Bochum, 2008.
3. Michael Szydlo. Merkle tree traversal in log space and time. Eurocrypt 2004, 2004.
4. Berstein et al. Post-Quantum Cryptography. Textbook. Springer, 2009.
