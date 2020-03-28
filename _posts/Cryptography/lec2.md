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

#### Ciphertext Only

* Statistics of Language
* Probable Words

#### Known Plaintext

* Common  Words  and  Phrases

* Previously Secret  Material

* Good  Certification  Assumption







