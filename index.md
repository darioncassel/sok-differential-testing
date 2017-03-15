## SoK: Software Testing Methods Applied to SSL/TLS: Lessons in Discovering Implementation Bugs

### Team Members
- Darion Cassel

### Motivation
There have been a diverse array of attemps at discovering bugs in implementations of SSL/TLS. We would like to present a taxonomy of these attempts in order to better understand what their essential features are, what classes of bugs these features are likely to capture, and what classes of bugs are blind spots for this field.

### Plan
1. Summarize and classify current approaches to bug detection for SSL/TLS
2. Determine essential features of each class and what kinds of bugs that feature can capture
3. Determine the 'coverage' of these classes; are there types of bugs that will not be caught by any approach?

### Papers
1. David Kaloper-Mersinjak, Hannes Mehnert, Anil Madhavapeddy and Peter Sewell. Not-quite-so-broken TLS: lessons in re-engineering a security protocol specification and implementation. USENIX Security 2015. [PDF](https://tlseminar.github.io/docs/nqsbtls.pdf) [Site](https://nqsb.io/)
  - Presents nqsb-TLS, code that is a specification of TLS, executable as a test oracle to check conformance of trances from arbitrary implementations.

2. Suman Jana, Yuan Kang, Samuel Roth, and Baishakhi Ray. Automatically Detecting Error Handling Bugs using Error Specifications. USENIX Security 2016. [PDF](https://tlseminar.github.io/docs/epex.pdf) [GitHub](https://github.com/yujokang/EPEx)
  - Presents EPEx, a tool that uses error specifications to identify and symbolically explore different error paths

3. Chad Brubaker, Suman Jana, Baishakhi Ray, Sarfraz Khurshid, Vitaly Shmatikov. Using Frankencerts for Automated Adversarial Testing of Certificate Validation in SSL/TLS Implementations. IEEE Symposium on Security and Privacy (Oakland) 2014. [PDF](https://tlseminar.github.io/docs/frankencerts.pdf) [GitHub](https://github.com/sumanj/frankencert)
  - Presents Frankencert, a tool for adversarial testing of certificate validation in SSL/TLS implementations

4. Benjamin Beurdouche, Antoine Delignat-Lavaud, Nadim Kobeissi, Alfredo Pironti, Karthikeyan Bhargavan. FLEXTLS: A Tool for Testing TLS Implementations. USENIX Workshop on Offensive Technologies, 2015. [PDF](https://tlseminar.github.io/docs/flextls.pdf)
  - Presents FLEXTLS, a tool for scripting and prototyping TLS scenarios for testing TLS implementations.
 
5. Juraj Somorovsky. Systematic Fuzzing and Testing of TLS Libraries. 2016. [PDF](https://www.nds.rub.de/media/nds/veroeffentlichungen/2016/10/19/tls-attacker-ccs16.pdf)
  - Presents TLS-Attacker, a framework for creating TLS message flows for fuzzing TLS servers
  
