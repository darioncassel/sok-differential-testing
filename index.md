## SoK: SSL/TLS Security Protocol Testing and Verification

Darion Cassel<sup>1</sup>, David Evans<sup>1</sup>

<sup>1</sup>University of Virginia, Charlottesville, Virginia, USA
<hr>


#### Abstract

TLS/SSL implementations have been shown to be have a slew of vulnerabilities that can lead to violation of its fundamental security properties. We classify current approaches to the testing of TLS implementations through the use of established taxonomies developed in the field of software engineering. Through the lens of this categorization, we assert that security protocol implementation vulnerabilities should be treated differently from traditional software faults because they are a violation of *two levels* of specification. On one level, they are an exceptional state of some protocol implementation’s state machine. On another level, they are a violation of a protocol model with the consequence of a violation of some security property that model intends to guarantee. No current approaches work on both levels but we believe that in order to discover a security fault and understand the implications of that fault without human intervention an approach must be able to combine information from both levels of testing. We present a cohesive workflow that combines two approaches in order to discover faults and classify the semantic threat of those faults concurrently.


### I. Introduction
TLS/SSL was originated by the company *Netscape* in 1994 in order to solve the problem of securing HTTP traffic [1]. Netscape never released version 1.0 of its Secure Sockets Layer protocol (SSL) due to the discovery of serious security flaws in the protocol, but went on to release version 2.0 to the public in February of 1995. However, this version too contained many security flaws, leading to a redesign of the protocol and the release of version 3.0 in 1996 [2]. The name Transport Layer Security (TLS) was given to an upgrade of SSL version 3.0 called TLS 1.0 which was defined in January 1999 and was not meant to be interoperable with SSL 3.0 [3]. 

Over the years many vulnerabilities have been found in TLS/SSL leading to the release of new versions of the protocol. TLS 1.1 was defined in April of 2006 in order to add protection against *cipher-block chaining* (CBC) attacks [4]. TLS 1.2 was defined in August of 2008 and it added *authenticated encryption*, removing the need for streaming and block ciphers and thus removing vulnerability to CBC attacks [5]. SSL 2.0 was formally deprecated in March of 2011. In June of 2011 the BEAST attack [6] exploting predictable *initialization vectors* IVs is released. Although the changes of TLS 1.1 and TLS 1.2 protect against BEAST, the adoption was slow of these updates leading to a large swath of vulnerable servers. In September of 2012 Duong and Rizzo release the CRIME attack which exploits TLS compression [7], leading to a rollback of that feature. In February of 2013 the Lucky Thirteen attack is released that executes *padding oracle* attacks against CBC suites [8]. In October of 2014 the publication of the POODLE attack that also leverages a padding oracle to perform a man-in-the-middle attack leads to SSL version 3.0 being considered insecure [9]. SSL version 3.0 is formally deprecated in June of 2015 [10]. Work on TLS version 1.3 has begun as of July of 2016.

According to a classic definition from the field of software engineering, software testing consists of the dynamic verification that a program provides expected behaviors on a finite set of test cases [11]. However, this definition is too coarse for our purposes as software testing has evolved into a wide and diversified field. The primary difference between various approaches to testing lies in the level of human involvement required to facilitate the testing. On one end of the spectrum lies manual code review, where an individual reads over the source code of a program and attempts to find faults in it [12]. At the other end of the spectrum is fully automated testing requiring no interaction from an individual once testing has commenced [12]. We will focus on the automated testing landscape. Within this subfield, several theories and methodologies have been extracted and used for the discovery of security vulnerabilities.

The main approaches to testing can be classified at a high level by their level of abstracion. At the lowest level there is dynamic analysis of a running system, followed by static analysis of a code base, and finally at the highest level there is model-checking. Despite there being a wide literature of testing security protocols for vulnerabilities, we find that the there is a clear separation to these approaches leading to inherent limitations for each category in terms the kinds of security vulnerabilities they can discover. Furthermore, there is a lack of a cohesive framework to place each approach into a distinct category such that its merits can be evaluated in terms of its peers.

We are motivated to provide a framework for the classification of security protocol testing and verification approaches in order to address these issues. We find that in the light of a granular framework, clear similarities and differences can be found within approaches. Correspondingly we also seek to provide a framework for the classification of the possible results of an approach. We believe that these combined classifications will explain what features of a particular approach are essential to the kinds of results it discovered, what features are common amongst approaches and may be abstracted into a larger framework, and which approaches can be combined to leverage multiple pieces of information to generate more meaningful results.

Our primary contributions are: (1) Establishing a framework for fine-grained classification of security protocol testing methodologies. (2) Determining a framework for the categorization and evaluation of the results of different testing approaches. (3) Providing a holistic comparison of multiple approaches on the basis of their classification and the classification of their results. (4) Proposing a 'combined approach' that leverages two or more distinct approaches to discover a security vulnerability at the level of an exceptional state of a protocol implementation and to determine the threat or meaning of the vulnerability by concurrently determining the way in which it violates the protocol model's security guarantees. (5) Identification of challenges for future research. 

In Section II, we provide background on TLS/SSL at the protocol level and define terminology for software testing and protocol verification. Then, in Section III, we give a classification of methodologies followed by a classification of possible results in Section IV. We provide a comparison of methodologies and results in Section V, and present a combined approach and its results in Section VI-VII. Finally we discuss related works in Section VIII and conclude with a discussion of possible future research in Section IX.


### II. Background and Definitions
- TLS/SSL background/definitions
- Software Testing background/definitions
- Protocol Verification background/definitions


### III. Methodology Classification 
- Classification of approaches


### IV. Result Classification


### V. Comparison of Methodologies and Results


### VI. [!] The Combined Approach


### VII. [!] Results with the Combined Approach


### VIII. Related Works


### IX. [!] Future Research


### X. References

[1] "THE SSL PROTOCOL". Netscape Corporation. 2007.

[2] Rescorla, Eric. SSL and TLS: Designing and Building Secure Systems. 2001.

[3] Polk, Tim; McKay, Terry; Chokhani, Santosh (April 2014). "Guidelines for the Selection, Configuration, and Use of Transport Layer Security (TLS) Implementations" 

[4] Dierks, T. & E. Rescorla (April 2006). "The Transport Layer Security (TLS) Protocol Version 1.1, RFC 4346".

[5] RFC 5246

[6] Thai Duong & Juliano Rizzo (2011-05-13). "Here Come The ⊕ Ninjas".

[7] Rizzo, Juliano; Duong, Thai. "The CRIME attack"

[8] Lucky Thirteen: Breaking the TLS and DTLS Record Protocols

[9] Bodo Möller, Thai Duong & Krzysztof Kotowicz. "This POODLE Bites: Exploiting The SSL 3.0 Fallback"

[10] RFC 6176

[11] P. Bourque and R. Dupuis, editors. Guide to the Software Engineering
Body of Knowledge Version 3.0 SWEBOK. IEEE, 2014.

[12] OWASP Testing Guide



Information from [Survey of Security Testing](https://blogs.uni-paderborn.de/sse/files/2015/08/book_chapter_sectesting.pdf?file=2015/08/book_chapter_sectesting.pdf)

