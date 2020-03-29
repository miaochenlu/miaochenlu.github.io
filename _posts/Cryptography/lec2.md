# Basic Notions of Cryptography

`Cryptography`{:.error}

Protection of  data  by  transformations that  turn  **useful  and  comprehensible plaintext** into **scrambled and meaningless  ciphertext**  under  control  of  **secret keys**.

### What Drives Cryptography?

Like   every   other   field   of   engineering,    cryptography   responds   to   the requirements   by   using   the   available tools, driven by the imaginations of its practitioners.

### Cryptography Guarantees Confidentiality

* Only authorized receivers who know secret keys can ***decrypt***
* Eavesddropper may intercept message, but ***cannot understand*** it.

### Cryptography Guarantees Authenticity

* Messgae sent by Intruder will decrypt to ***nonsense***.
* Intruder may inject messages, but cannot 'get them accepted'.

### Cryptographic  System

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200327225218954.png" alt="image-20200327225218954" style="zoom:50%;" />



### Representation of a Cipher

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200327225309964.png" alt="image-20200327225309964" style="zoom:50%;" />

### Representation  of Encryption

Unkeyed map from plaintexts to plaintext  representations.

Keyed  map  from  plaintext  representations  to ciphertext  representations.

Unkeyed   map   ciphertext   representations  to ciphertext.



### Plaintext

Information  In  Usable and  Comprehensible Form

Example

> *  Written  human  language
>
> * Spoken  Language
>
> *  Accounting  Legers
>
> * Seismic  Data地震数据
>
> * Computer  program
>
> * Formal  commands  (EFT, $C^2$)
>
> * Somebody  else’s  ciphertext



#### Representations of Plaintext

* Messages  drawn from  a  fixed set

* Fixed  length  strings  over  an  alphabet

* Finite  length  strings  ...

#### Characteristics of Plaintext

* Message  (e.g., letter)  frequencies

* Pair  and  triple  frequencies

* Contact  characteristics[vowel]

* Gramatical  restrictions[左右括号]



### Ciphertext

Information  in  Useless Scrambled  Form

#### Characteristics  of Ciphertext

Unpredictable (random) at some level.  Scrambled and Useless

* Unreadable

* ‘Destroyed’ by Alteration

* Looks Like Random Noise



### Substitution-example

Permutation   of   the   space   of   plaintexts.

#### Full Subsititution

Plaintext  space,  or  message  space,  or alphabet: M of n elements.

$$k:M\mapsto M$$

Number of keys =(Card(M))! where each $k\in S_{card(M)}$

#### Examples  of Full  Substitution

Monoalphabetic substitution on Roman  (or  ASCII,  etc.)   alphabet:  S-box.

$$k:\{A,\cdots,Z\}\mapsto\{A,\cdots,Z\}$$



Prearranged  message code

sell  short			TXDNL

go  public			PULID

fire  CEO			 HADOS

file  Chap.  11	 AMBIT

#### Maximal  and Minimal  Substitutions

Minimal Substitution: The cyclic Shifts

Let $a\rightarrow 0, b\rightarrow 1,\cdots$ and let $k\in\{0,\cdots,25\}$ map $l\mapsto l+k$

For any set $\{p_1,\cdots,p_n\}$ of plaintexts and any set of ciphertexts $\{c_1,\cdots,c_n\}$ there is a key such that $k(p_i)=c_i$

#### Transposition

Permute Elements of Message

w h a t i s i n i t f o r m e

h m i i t s i o t w r n e a f



### General Systems and Specific Keys

* Cryptographic  Systems

Standardized  and  Public

* Cryptographic  Keys

Unique  and Secret

Like  Physical Locks  and Keys



### Cryptographic  Systems

* Standardized

* Economy of Scale

* In Principle Public



### Cryptographic  Keys

* Very  Large  Supply

* Each  Key Unique

* Must  Be Kept  Secret

***All  Secrecy  Resides  In Key***

#### Primary and Secondary Keys

The  primary  key  is  the  one  changed most  frequently,   perhaps  with  every message.

The secondary key is changed less frequently,  perhaps  every  day,  week,  or month.

### Cryptanalysis

Produce Plaintext  from  Ciphertext  (Analytic)

or

Produce Ciphertext  from  Plaintext  (Synthetic)

(Without  Prior  Knowledge of  the  Key)



### Analytic vs. Synthetic Cryptanalysis

Synthetic  crypta  can  be  much  harder than  analytic.

#### Analytic Cryptanalysis

* Extract Information from  Cryptograms

* Violates Confidentiality

* Better  Known  Problem

#### Synthetic  Cryptanalysis

* Create  False Messages

* Violates Authenticity

* Harder  Problem

### Rolling Code Speech Scrambler

* Filter  Speech  into 5  bands.

* Rearange  the Bands.

* Change   Rearangement   5   times a second.

### Applications of  Cryptanalysis

* Production of  Intelligence

* Certification  of Systems

### Cryptanalytic Circumstances

Cryptanalyst is  Always Presumed to  Know  the General  System

* Matter  of Definition

* Sound  Security  Practice

### Types of  Attack

* Ciphertext  Only

* Known  Plaintext

* Chosen  Plaintext

<br>

#### Ciphertext Only

* Statistics of Language
* Probable Words

#### Known Plaintext

* Common  Words  and  Phrases

* Previously Secret  Material

* Good  Certification  Assumption

#### Chosen Plaintext

* Best Certification Assumption
* Either Plain or Ciphertext

### Oneway Functions

* Easy to compute forward
* Hard to Go Back

#### Easy to Raise Numbers to Powers

$x\rightarrow x^7$

#### Hard to Extract Roots

$x\rightarrow \sqrt[7]{x}$

#### Easy to Multiply

$127\times 997=126619$

#### Hard to Factor

126619

### Public Key Cryptography

* Keys Come In Inverse Pairs
* Given One--Can't Find the Other

#### Fundamental Property

* Public Key -- In telephone directory(everyone can see)
* Secret Key -- In subscriber's safe

#### Solves Both Problems

* Key Distribution: Encrypt with public key
* Digital Signature: Encrypt with secret key

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200329231420080.png" alt="image-20200329231420080" style="zoom:50%;" />

<br>

### Information Theory

* Information resolves uncertainty
* Perfect secrecy
* Ideal secrecy

### Finite Automaton

A  finite  automaton  consists  of  a  set of  states S,  a  set  of  inputs I,  a  set  of outputs O, and a distinguished starting state S0,  together  with  two  rules:   a change  of state  map:

CS:I×S→S

and  an output  map:

$Out:S\mapsto O\quad Moore$

$Out:S\times O\mapsto O\quad Mealy$

### Computational Complexity

Cost  of  performing  a  computation  in instructions,  memory  locations  gates, dollars,  etc.   as  a  function  of  the  size of  the input.

Possible  Measures



* Worst  Case

* Average  Case  —  Hard  to  measure

(What  are ‘typical’ inputs?)

* Easiest  Case  —  Hard  to define

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200329232324833.png" alt="image-20200329232324833" style="zoom:50%;" />

Selecting  a  subset  of  the  elements  of a 'cargo' vector and adding them up is easy.

Given a cargo vector, and a number it is hard to find a subset that add up to that  number.

<a href="ftp://im1.im.tku.edu.tw/Prof_Liang/%BA%F4%B8%F4%A6w%A5%FE/%AD%5E%A4%E5%AA%A9/Crypto4e-Appendices/f-knap~7.pdf">for reference</a>



First, let us state the general approach for encryption/decryption using the knapsack problem. Suppose we wish to send messages in blocks of *n* bits. Then, define:

cargo vector **a** = (*a*1, *a*2, ..., *an*)  *ai* integer 

plaintext message block **x** = (*x*1, *x*2, ..., *xn*)  *xi* binary

corresponding ciphertext *S* = **a** • **x** = $\sum_{i=1}^n(a_i \times x_i )$

<br>

Consider the cargo vector **a** to be a list of potential elements to be put in the knapsack, with each vector element in **a** equal to the weight of the corresponding cargo element. And consider the plaintext message block **x** to be a selection of elements from the cargo vector, with *xi* = 1 for

each cargo element *ai* that is selected for inclusion in the knapsack. Thus each unique plaintext

message block corresponds to a unique selection of items from the cargo vector. The vector product *S* is simply the sum of the selected items, which is the weight of the knapsack.

For encryption, **a** is the public key. If Bob wishes to send a confidential message **x** to Alice, Bob encrypts the message using Alice's public key **a**. Bob performs *S* = **a • x** and transmits *S*. For decryption, the Alice must recover **x**, given *S* and **a**.

This public-key scheme must satisfy two requirements. The **first requirement** is that there be a unique inverse for each value of *S*. For example, consider the following:

**a** = (1, 3, 2, 5) *S*= 3

<br>

* Worst  case  is NP-complete.

* Average  case  is  though  to  be  hard but  nothing is  known  for  sure.

* Easiest  case  is  trivial:    binary  decomposition.



### Degrees of Difficulty

* A problem is considered 'Easy' if it can be  done  in  time  that  is  a  polynomial function  of the  length  of the  input.

* A   problem   can   be   solved   in non-deterministic polynomial time if the answer can be checked in polynomial time. I.e., you could do it in polyno-mial time if you guessed the answer.

* A problem is said to be NP-Complete if any polynomial time technique for solving it could be used to solve all NP-time problems in polynomial time.