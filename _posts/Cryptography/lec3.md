# Cryptography:  Post WWI  to Just  After WWII

### The  Essence  of  Security

* Recognition  of those  you  know

* Introduction   to   those   you   don’t know

* Written  Signature

* Private  Conversation



Communication  security  is  the  transplantation  of  these  basic  social  mechanisms to the telecommunications environment.



### Privacy or Confidentiality

Guarantee  to Sender

Authorized  Receivers Only

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200331225532200.png" alt="image-20200331225532200" style="zoom:50%;" />

#### Other  Confidentiality Objectives

* Transmission Security

* Traffic  Confinement



### Authenticity  and Integrity

Guarantee  to Receiver

* Knows  Identity  of  Sender

* Message  Not Altered

* Not  Unduly Delayed

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200331225743357.png" alt="image-20200331225743357" style="zoom:50%;" />

### Types of  Intrusion

* Message  Insertion

* Replay

* Message  Modification

  * With  known  results

  * With unknown results (e.g., message  rearrangement)

* Denial  of Service

### Confidentiality  vs.  Authenticity

* EFT  — Authenticity

* Satellite Vidoeconference — Confidentiality



### Confidentiality  and Authenticity Interact

* Phony  caller  can  ask  for  secret  information

* Would  be  spoofers  can  read  traffic and  study network  operations.



### Approaches  to Protection

* Pressurized Conduit

* Optical  Fiber

* Line  of Sight  Laser

* Damped frequencies

* Spread  Spectrum

* Cryptography

<br>

### Cryptography

Protection of  data  by  transformations that  turn  useful  and  comprehensible plaintext into scrambled and meaningless  ciphertext  under  control  of  secret keys.

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200331231317582.png" alt="image-20200331231317582" style="zoom:50%;" />

#### Cryptography  Guarantees Confidentiality

* Only authorized receivers who know secret keys can  decrypt

* Eavesdropper   may   intercept   message,  but cannot  understand it.

#### Cryptography  Guarantees Authenticity

* Message  sent  by  Intruder  will  decrypt  to nonsense.

* Intruder  may  inject  messages,  but cannot  'get them  accepted.'



p20

### Enigma

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200401214957901.png" alt="image-20200401214957901" style="zoom:50%;" />



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200401215545345.png" alt="image-20200401215545345" style="zoom:50%;" />

#### Keystream Generator Requirements

* Long  period

* High  Complexity

* (Not  always  compatible)

