<style TYPE="text/css">
code.has-jax {font: inherit; font-size: 100%; background: inherit; border: inherit;}
</style>
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
    tex2jax: {
        inlineMath: [['$','$'], ['\\(','\\)']],
        skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'] // removed 'code' entry
    }
});
MathJax.Hub.Queue(function() {
    var all = MathJax.Hub.getAllJax(), i;
    for(i = 0; i < all.length; i += 1) {
        all[i].SourceElement().parentNode.className += ' has-jax';
    }
});
</script>
<script type="text/javascript" src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>


## SoK: SSL/TLS Security Protocol Testing and Verification

Darion Cassel<sup>1</sup>, David Evans<sup>1</sup>

<sup>1</sup>University of Virginia, Charlottesville, Virginia, USA
<hr>


#### Abstract

TLS/SSL implementations have been shown to be have a slew of vulnerabilities that can lead to violation of its fundamental security properties. We classify current approaches to the testing of TLS implementations through the use of established taxonomies developed in the field of software engineering. Through the lens of this categorization, we assert that security protocol implementation vulnerabilities should be treated differently from traditional software faults because they are a violation of *two levels* of specification. On one level, they are an exceptional state of some protocol implementation’s state machine. On another level, they are a violation of a protocol model with the consequence of a violation of some security property that model intends to guarantee. No current approaches work on both levels but we believe that in order to discover a security fault and understand the implications of that fault without human intervention an approach must be able to combine information from both levels of testing. We present a cohesive workflow that combines two approaches in order to discover faults and classify the semantic threat of those faults concurrently.


### I. Introduction
TLS/SSL was originated by the company *Netscape* in 1994 in order to solve the problem of securing HTTP traffic [1]. Netscape never released version 1.0 of its Secure Sockets Layer protocol (SSL) due to the discovery of serious security flaws in the protocol, but went on to release version 2.0 to the public in February of 1995. However, this version too contained many security flaws, leading to a redesign of the protocol and the release of version 3.0 in 1996 [2]. The name Transport Layer Security (TLS) was given to an upgrade of SSL version 3.0 called TLS 1.0 which was defined in January 1999 and was not meant to be inter-operable with SSL 3.0 [3]. 

Over the years many vulnerabilities have been found in TLS/SSL leading to the release of new versions of the protocol. TLS 1.1 was defined in April of 2006 in order to add protection against *cipher-block chaining* (CBC) attacks [4]. TLS 1.2 was defined in August of 2008 and it added *authenticated encryption*, removing the need for streaming and block ciphers and thus removing vulnerability to CBC attacks [5]. SSL 2.0 was formally deprecated in March of 2011. In June of 2011 the BEAST attack [6] exploiting predictable *initialization vectors* IVs is released. Although the changes of TLS 1.1 and TLS 1.2 protect against BEAST, the adoption was slow of these updates leading to a large swath of vulnerable servers. In September of 2012 Duong and Rizzo release the CRIME attack which exploits TLS compression [7], leading to a rollback of that feature. In February of 2013 the Lucky Thirteen attack is released that executes *padding oracle* attacks against CBC suites [8]. In October of 2014 the publication of the POODLE attack that also leverages a padding oracle to perform a man-in-the-middle attack leads to SSL version 3.0 being considered insecure [9]. SSL version 3.0 is formally deprecated in June of 2015 [10]. Work on TLS version 1.3 has begun as of July of 2016.

According to a classic definition from the field of software engineering, software testing consists of the dynamic verification that a program provides expected behaviors on a finite set of test cases [11]. However, this definition is too coarse for our purposes as software testing has evolved into a wide and diversified field. The primary difference between various approaches to testing lies in the level of human involvement required to facilitate the testing. On one end of the spectrum lies manual code review, where an individual reads over the source code of a program and attempts to find faults in it [12]. At the other end of the spectrum is fully automated testing requiring no interaction from an individual once testing has commenced [12]. We will focus on the automated testing landscape. Within this subfield, several theories and methodologies have been extracted and used for the discovery of security vulnerabilities.

The main approaches to testing can be classified at a high level by their level of abstraction. At the lowest level there is dynamic analysis of a running system, followed by static analysis of a code base, and finally at the highest level there is model-checking. Despite there being a wide literature of testing security protocols for vulnerabilities, we find that the there is a clear separation to these approaches leading to inherent limitations for each category in terms the kinds of security vulnerabilities they can discover. Furthermore, there is a lack of a cohesive framework to place each approach into a distinct category such that its merits can be evaluated in terms of its peers.

We are motivated to provide a framework for the classification of security protocol testing and verification approaches in order to address these issues. We find that in the light of a granular framework, clear similarities and differences can be found within approaches. Correspondingly we also seek to provide a framework for the classification of the possible results of an approach. We believe that these combined classifications will explain what features of a particular approach are essential to the kinds of results it discovered, what features are common amongst approaches and may be abstracted into a larger framework, and which approaches can be combined to leverage multiple pieces of information to generate more meaningful results.

Our primary contributions are: (1) Establishing a framework for fine-grained classification of security protocol testing methodologies. (2) Determining a framework for the categorization and evaluation of the results of different testing approaches. (3) Providing a holistic comparison of multiple approaches on the basis of their classification and the classification of their results. (4) Proposing a 'combined approach' that leverages two or more distinct approaches to discover a security vulnerability at the level of an exceptional state of a protocol implementation and to determine the threat or meaning of the vulnerability by concurrently determining the way in which it violates the protocol model's security guarantees. (5) Identification of challenges for future research. 

In Section II, we provide background on TLS/SSL at the protocol level and define terminology for software testing and protocol verification. Then, in Section III, we give a classification of methodologies followed by a classification of possible results in Section IV. We provide a comparison of methodologies and results in Section V, and present a combined approach and its results in Section VI-VII. Finally we discuss related works in Section VIII and conclude with a discussion of possible future research in Section IX.


### II. Background and Definitions

#### Background on SSL/TLS

The TLS protocol is designed to provide encryption, authentication, and data integrity to the parties using the protocol. In order for this to occur, the two parties first participate in the TLS *handshake protocol* which includes a component of *certificate validation*. Once that is complete data is exchanged though the use of the *record layer protocol*. Most efforts to test and verify TLS have found vulnerabilities in the handshake protocol and the certificate validation phase. As such we will focus on describing them in detail here.

*A. The TLS Handshake*

Let us consider two parties, Alice and Bob. The primary goal of the handshake is to provide "privacy and reliability" [CITE: verifying SSL] for Alice and Bob's communications through the establishment of secret keys. 

<center><img src="https://hpbn.co/assets/diagrams/b83b75dbbf5b7e4be31c8000f91fc1a8.svg" alt="TLS Handshake" style="width:800px;"/><br>
<sup>TLS Handshake</sup></center>

At a fundamental level, the handshake consists of three messages. The first is the *ClientHello* message with which Alice initiates the protocol, transmits her SSL/TLS version number and sends a set of cryptographic algorithms she supports called the *ciphersuite*. 

Bob will then reply with the *ServerHello* message that has his choice of TLS protocol version, a choice of ciphersuite, and his certificate. When Alice receives this messages she must perform certificate validation on the Bob's certificate, and must agree to the choices of protocol version and ciphersuite. 

If Alice accepts Bob's certificate and accepts the shared SSL/TLS version and cipher, then the client initiates a *key exchange protocol*, namely RSA or Diffie-Hellman, which allows for the establishment of a symmetric key to be used for the rest of the connection.

// CHECKTHIS
It should be noted that SSL 3.0 onwards supports *session resumption*. During the first run of the TLS protocol between Alice and Bob, they go through the full handshake protocol as detailed above. However, on future connections, session resumption allows for reconnection without use of the an abbreviated handshake. Specifically, if Alice sends a ClientHello message that includes a *session identifier* of a session that Bob has recorded as active, then Bob will assume that Alice wants to resume that previous session. A new secret is not exchanged, instead the previous keys are recomputed with new nonces and used. 

*B. Certificate Validation*

X.509 certificate validation is perhaps the most complex piece of the TLS handshake protocol. It involves the use of a transitive process called *chain of trust verification*. Each SSL/TLS client trusts a certain number of certificate authorities (CAs) whose X.509 certificates are usually included with the client's Operating System or browser. [CITE: frankencerts] This is considered to be the client's *root of trust*.

<center><img src="https://hpbn.co/assets/diagrams/bb75b8bd469ce5b703b76abb7042e978.svg" alt="Chain of Trust" style="width:800px;"/><br>
<sup>Certificate Chain of Trust</sup></center>

Each X.509 certificate that the client receives from a server it is attempting to connect with has an *issuer* field that contains the name of the certificate authority (CA) that issued the certificate. This server certificate is required to be accompanied by the certificate of the issuing CA, and if that issuer is not a *root CA*, then all of the certificates up to the root CA need to be included. It is this chain of certificates going from a server certificate to a root CA, the chain of trust, that needs to be validated by the client. Due to the complexity of the X.509 certificates themselves, this is not a simple task. Some of the checks involved in validating the chain are [CITE: RFC 5280]:

- Checking that each certificates in the chain is signed by a CA immediately above it and the root of the chain is a client's trusted root CA.
- If the certificate is a version 1 or version 2 certificate then the client must either reject it or verify it by out-of-band means
- Certificates can have extensions. Each of these is either designated as *critical* or *non-critical*. A certificate with an unrecognized (by the client) critical extension must be rejected.
- IF a certificate contains a *name constraints* extension, then the subject name in the next certificate in the chain must satisfy those name constraints. This is considered a critical extension.
- //ADDMORE

It is due to this complexity that the certificate validation phase of the TLS protocol is a hotbed of security vulnerabilities.

*C. The Record Protocol*

Similarly to TCP or IP, data that is exchanged within a TLS session is framed using a protocol. The record protocol allows for the identification of different types of messages, either handshake, alert, or data, via the *Content Type* field, and includes authentication of the encrypted data via a *message authentication code* (MAC).

<center><img src="https://hpbn.co/assets/diagrams/4603275cd98c93aeb8c46b1b1afa0ba6.svg" alt="TLS Record Structure" style="width:800px;"/><br>
<sup>TLS Record Structure</sup></center>

The process for delivering application data using the record protocol is that the data is divided into blocks of a maximum size of 16 KB. A MAC or HMAC is then added to each block and the data is encrypted using the ciphersuite that was negotiated during the handshake. Depending on the ciphersuite being used, padding might be added to the data. The reverse of this process is used by the client when it receives a frame of data.

#### Software Security Testing Background and Definitions

In a classical sense, *software testing* consists of the dynamic verification that a program provides expected behaviors on a finite set of *test cases* called a *test suite*. Closely related is the notion of *dynamic testing* that evaluates software by observing its execution. On the other hand, *Static testing* checks *software development artifacts* (i.e requirements, design, or code) without the execution of these artifacts. Static testing is usually performed either through *manual reviews* or *automated analysis*.

After a particular test case is executed, the observed and intended behaviors of the *software under test* (SUT) are compared, resulting in a *verdict* that is either:
- *Pass*: the behaviors conform
- *Fail*: the behaviors do not conform
- *Inconclusive*: it cannot be determined whether the behaviors conform.

The mechanism that is used for determine the verdict is called the *test oracle*. If the SUT is being checked for compliance with user or customer needs this is called *validation*. If the SUT is being tested against a specification this is called *verification*.

In the event of a *failure*, which is an undesired behavior that either fails validation or verification, a cause of failure called a *fault* may be assigned to it.

A fault can be either:
- A *static defect* in the software caused by human error in the specification, design or coding process.
- A *behavioral description* of how the software deviates from the intended behavior.
- A *vulnerability*, which is fault that is a *security flaw* and results in a violation of a security property.

The meaning of vulnerability is one of two things, either the security mechanism necessary for protecting against that vulnerability is missing, or the security mechanism has been implemented in a faulty way.

The *exceptional element* is an the input that provokes an undesirable behavior in the SUT. The exceptional element can violate a protocol specification or it can sometimes be a legal input that exploits an ambiguity in the specification.

The *instrumentation* used to monitor the SUT can be divided into two categories, *out-of-band instrumentation*, such as debuggers, and *in-band instrumentation*, which is added to the program via the test *injection vector*, the same interface used to give the test case to the SUT.

*Security testing* verifies and validates software system requirements related to security properties. Typical security properties under test are:
- *Confidentiality*, which is the assurance that information is not disclosed to unauthorized individuals, processes, or devices
- *Integrity*, which is provided when data is unchanged from its source and has not been accidentally or maliciously modified, altered, or destroyed. 
- *Availability*, which guarantees timely, reliable access to data and information services for authorized users.
- *Authentication*, which is a security measure designed to establish the validity of a transmission, message, or originator, or a means of verifying an individual’s authorization to receive specific categories of information. 
- *Authorization*, which provides access privileges granted to a user, program, or process.
- *Non-repudiation*, which is the assurance that none of the partners taking part in a transaction can later deny of having participated.

There are several paradigms for performing security testing depending on what stage during the application's development the testing occurs. However, it should be noted that testing methods from earlier stages can be used for later stages but methods from later stages cannot be used during earlier stages of the application development lifecycle.
- *Model-based* security testing is grounded on requirements and design models created during the analysis and design phase. *Model-based testing* (MBT), manually selected algorithms automatically and systematically generate test cases from a set of models of the system under test or its environment
- *Code-based* testing and static analysis on source and bytecode created during development
- *Penetration testing and dynamic analysis* on running systems, either in a test or production environment
- *Security regression testing* performed during maintenance.

The *test scope* describes the granularity with which the SUT is being evaluated. Usually, this can be divided into *component*, *integration*, *system*, or *regression* testing:
- Component testing, also referred to as *unit testing*, checks the smallest testable software component in isolation.
- Integration testing tests a set of combined components.
- System testing tests the system as a whole, including and subsets of components.
- Regression testing is a form of selective testing that checks that certain software modifications have not resulted in faults.

A *test plan* is a set of *test objectives*, *test scope*, and *test methods* that identifies features to be tested and *exit criteria* that define conditions when testing will end. A typical form of *exit criteria* is *coverage*, which measures how much of a particular program is reached during testing.

The *accessibility* of software development artifacts under test is a measure of how much information is available *a prior* about the SUT.
- *White-box testing* uses test cases that are derived based on information about how the software has been designed or coded.
- *Black-box testing* uses test cases that rely only on the input/output behavior of the software is available. It is frequently used in security testing where testing is often done to mimic external attacks from an adversary.

As noted, black-box testing does not require access to source code or other development artifacts of the SUT. Testing is typically performed via interaction with the SUT using:
- *Fuzzing*, which feeds random data to a program "until it crashes"
- *Mutation-based fuzzing*, where the fuzzer has knowledge about the input format of the program under test, such as existing data samples.
- *Generation-based fuzzing*, which uses a model of the input data for generating test data.
- *Concolic testing*, which combines symbolic execution that is a static source code analysis technique with dynamic testing.

#### Background/Definitions on Protocol Verification

*Abstract static analysis* is the automatic computation of information about the behavior of a program without executing. This kind of analysis is usually used to compute *approximate* but *sound* conjectures about the behavior of a program. We say that are sound because they are guaranteed to not be misleading.

The analysis process is vulnerable to two kinds of errors:
- *A spurious warning*, which is an error message about a fault that does not exist in the program.
- *A missed bug*, which means that the analysis procedure did not report a fault that actually exists in the program. 

We say that a program analysis method is *flow sensitive* if the order of execution of statements in the program is considered, *path sensitive* if the analysis distinguishes between paths through a program and attempts to only consider feasible ones, *context sensitive* if method calls are analyzed differently based on the call site, and *inter-procedural* if the bodies of method calls are also analyzed.

Protocol verification usually makes use of the *software model checking* methodology. This consists of a *model* of the program, which is a set of *states*, such as the evaluation of the program counter, the values of program variables, and the configuration fo the stack and heap, and a set of *transitions* that describe how the programs moves from one state to another.

In the context of model-checking, a *specification*, or *correctness property* is a literal logical formula of states and transitions. If a state violating a specification is found, a *counterexample*, which is an execution trace demonstrating the error, is produced.

Thus, we say that a model checker can return one of three results:
- *Pass*, indicating that the property is satisfied
- *Fail*, indicating that the property is not satisfied and that a counterexample exists
- *Inconclusive*, the model checker cannot compute verification in a "reasonable" amount of time

Model checking tools verify *partial specifications* that are classified as *safety*, which expresses the unreachability of bad states, and *liveness*, which specifies that a "good" state is eventually reached, such as a request to a webserver being served eventually.

The principle problem with vanilla model checking is *state-space explosion*, which means that the state-space of a software program is exponential in terms of its various parameters such as the number of variables and the width of the datatypes.

The state-space explosion is particularly a problem with *explicit-state model checking algorithms* that directly index states and use graph algorithms to explore the state space. Some efficiency may be gained by checking for property violation *on-the-fly* with new states, such that the entire graph does not have to be built to perform the analysis. Other improvements include the compression of explored states that are then stored in a hashtable to ensure that their successors are not recomputed. Finally, *partial order reduction* is used to prune the state space exploration of concurrent programs.

An alternative to explicit-state model checking algorithms is *symbolic model checking algorithms* that use implicit representations of sets of states. Common symbolic representations are 
- *BDDs*, which are obtained from a *boolean decision tree* and permit boolean functional equivalence for efficient checking
- *Propositional logic for finite sets*, which are more memory efficient at the cost of computation time
- *Finite automata for infinite sets*

Another alternative is *bounded model checking* (BMC). This technique involves "unwinding" the model under verification *k* times together with a property to form a propositional formula that is then passed to a SAT solver. The result of the test is viewed as a *pass* if and only if there is a trace of length *k* that refutes the property. The result is viewed as *inconclusive* if the formula is unsatisfiable. The way BMC is implemented is to treat the entire program as one transition relation by adding a program counter variable to the model. One transition of the design corresponds to one basic block of the program. In each transition, the next program location is computed by following the control flow graph (CFG) of the program. The basic block is converted into a formula by transforming it into Static Single Assignment (SSA) form. The arithmetic operators in the basic block are converted into simplistic circuit equivalents. Arrays and pointers are treated as in the case of memories in hardware verification: a large case split over the possible values of the address is encoded.

There is a level of care to be given to the models themselves. We say that a model of a protocol is a *symbolic model*, often called the Dolev-Yao Model is the cryptographic primitives are represented by function symbols considered as black-boxes, messages are terms on these primitives, the adversary is restricted to using these primitives, and the model assumes perfect cryptography. On the other hand, in the *computational model* messages are bitstrings, the cryptographic primitives are functions from bitstrings to bitstrings, the adversary is a probabilistic Turing machine, and a security property is considered to hold when the probability that it does not hold is negligible in the security parameter.

Finally, we say that *trace properties* are properties that can be defined on each execution trace of the protocol, and *equivalence properties* mean that an adversary cannot distinguish two processes.


### III. Methodology and Result Classification 

We shall be looking at the following approaches:
- "Using Frankencerts for Automated Adversarial Testing of Certificate Validation in SSL/TLS Implementations" [Brubaker]  (*Frankencerts*)
- "Finite-State Analysis of SSL 3.0" [Mitchell]  (*Finite-State Analysis of SSL 3.0*)
- "Not-quite-so broken TLS: lessons in re-engineering a security protocol specification and implementation" [Kaloper-Mersinjack]  (*Not-quite-so-broken TLS*)
- "A Messy State of the Union: Taming the Composite State Machines of TLS" [Beurdouche]  (*A Messy State of the Union*)
- "Systematic Fuzzing and Testing of TLS Libraries" [Somorovsky]  (*Systematic Fuzzing and Testing of TLS Libraries*)
- "Protocol state fuzzing of TLS implementations" [Ruiter]  (*Protocol state fuzzing of TLS implementations*)
- "Testing Embedded TLS Implementations Using Fuzzing Techniques and Differential Testing" [Walz]  (*Testing Embedded TLS Implementations*)
- "Goanna—A Static Model Checker" [Fehnker]  (*Goanna—A Static Model Checker*)

#### Framework for Methodology and Result Classification

In order to classify methodologies, we shall be using the following approach, first we will determine the *type* of testing, which will be one of:
- *Dynamic Testing*
- *Static Testing*

If an approach uses dynamic testing, it can be further classified into:
- *Penetration testing*
- *Fuzzing*

If fuzzing is being used, it can be specified either as:
- *Random fuzzing*
- *Mutation-based fuzzing*
- *Generation-based fuzzing*
- *Concolic testing*

If the approach uses static testing, it can be classified into:
- *Model-checking*
- *Code-based analysis*

If model-checking is being used, it can be specified as:
- *Explicit-state model-checking*
- *Symbolic model-checking*
- *Bounded model-checking*

The model used in model-checking is either:
- *Manually specified*
- *Automatically generated*

Next, the *exceptional element* and the *test oracle* shall be categorized. The exceptional element can be portrayed either as:
- An *execution trace*
- An *exceptional input*

The *test oracle* can be:
- A *reference implementation*
- A *specification*
- *Manual inspection*

The *reference implementation* and the *specification* are either:
- *Manually determined*
- *Automatically generated*

Also in consideration are the *test scope* and the *exit criteria*. The test scope can be either
- *Component testing*
- *Integration testing*
- *System testing*
- *Regression testing*

The *exit criteria* can be:
- *Manual*
- *Exhaustion*
- *Coverage*

For dynamic testing methods, the *injection vector* and *instrumentation* are considered. The injection vector and can be one of:
- *Server interaction*
- *Simulated interaction*

The *instrumentation* can be
- *Out-of-band*
- *In-band*

Additionally, we consider the *accessibility* of test artifacts as either:
- *White-box testing*
- *Black-box testing*

We classify whether the methodology is vulnerable to *spurious warnings* or *missed bugs* and consider the form of vulnerabilities received:
- *Exceptional traces*
- *Manually-inspected behavior*
- *Source code correspondences*
- *Counterexamples* (for model checking)

Finally, we consider how the discovered vulnerabilities are corresponded to violations of security properties:
- *Manually*
- *Automatically*

To summarize, each methodology shall be classified using these metrics:

(8) Core Properties
1. Type
2. Test Scope
3. Pre-test Accessibility
4. In-test Accessibility
5. Post-test Accessibility
6. Exceptional Element
7. Test Oracle
8. Exit Criteria

(3) Dynamic Testing
1. Type
2. Injection Vector
3. Instrumentation

(2) Static Testing
1. Type
2. Model Generation

(4) Results 
1. Spurious Warnings?
2. Missed Bugs?
2. Form of Vulnerabilities
3. Threat Assessment of Vulnerabilities

#### Classifications

*A. Frankencerts*

The Frankencerts paper implemented a methodology for large-scale testing of certificate validation logic in SSL/TLS implementations through the use of randomly generated synthetic certificates built from parts of real certificates. The authors then applied differential testing to uncover discrepancies between several SSL/TLS implementations such as OpenSSL, NSS, CyaSSL, GnuTLS, PolarSSL, and MatrixSSL. 208 discrepancies were identified, which could be attributed to 15 distinct root causes.

##### 1. Type
The Frankencerts paper uses a *dynamic testing approach*. Specifically, it uses *mutation-based fuzzing* wherein a sample of input data is permuted to form new test cases.

##### 2. Accessibility

This methodology uses *black-box testing*; the tests have no knowledge of the source code; the SUT is viewed purely as an input/output machine.

##### 3. Test Scope

The scope is *system testing*. Although the authors are attempting to test just certificate validation, they must run the full implementations as the SUT and could theoretically receive faults from other parts of the system than the certificate validation subsystem.

##### 4. Exceptional Element

The exceptional element is presented as an *exceptional input*, in this case a "frankencert" which is a X.509 certificate automatically generated from a set of sample input data (other certificates).

##### 5. Injection Vector

The injection vector is *server-interaction*. The authors set up a server that would present connecting clients (running one of the SSL/TLS libraries) with a frankencert.

##### 6. Instrumentation

The instrumentation is *in-band*. The clients set up by the authors record the answers, including error codes, given by the SSL/TLS implementations when presented with the frankencert.

##### 7. Test Oracle

The test oracle is a *reference implementation*, but is a bit more nuanced than that; in the case of this approach the test oracle is discrepancies between the behavior of the implementations, given that each implementation is presented with the same frankencert.

##### 8. Specification

Since there is no single reference implementation, the specification must be *manually determined*. That means, for each discrepancy uncovered the authors must manually determine which of the conflicting behaviors is correct and what specifically is the fault.

##### 9. Exit Criteria

The exit criteria is a form of *exhaustion*. Once all of the test case data is exhausted, the testing ends.

##### 10. Vulnerability to *spurious Warnings

This method is *not vulnerable* to *spurious warnings. Since differential testing is being employed, any discrepancy must be an actual fault in one of the implementations since they all purport to implement the same specification.

##### 11. Form of Vulnerabilities

The form of vulnerabilities is *exceptional traces*, as mentioned, answers and error codes from the implementations are recorded.

##### 12. Threat Assessment of Vulnerabilities

The threat assessment of vulnerabilities must be done *manually*. If a given implementation has a discrepancy, it must be carefully tested (manually) to determine what caused that discrepancy.


*B. Not-quite-so broken TLS*

With the Not-quite-so-broken TLS, the authors of the paper sought to build a reference TLS implementation (nqsb-TLS) built through a re-engineered approach to security protocol specification and implementation. Although the aim of the paper was not the explicit testing of a TLS implementation, the authors' reference implementation can be used as a test oracle and was used for testing.

##### 1. Type

The type of testing was *dynamic testing*, specifically *mutation-based fuzzing* and *penetration testing*. In the first case, the Frankencert method was used to generate 10,000 X.509 certificate chains and these were given to nqsb-TLS. In the second case, the authors set up a publicly accessible server running nqsb-TLS and set a bounty for the successful compromise of the server.

##### 2. Accessibility

Both forms of testing were *black-box testing*, neither the frankencert method nor server attackers had access to the source code during the trials.

##### 3. Exceptional Element

Both forms of testing used *exceptional inputs*. The mutation-based fuzzing fed mutated certificates and the penetration testing gave inputs to the server.

##### 4. Injection Vector

The injection vector in both cases was through *server-interaction*.

##### 5. Instrumentation

The instrumentation was an *out-of-band*, the authors could monitor whether the implementation accepted the frankencerts and could track attack traces on the public server.

##### 6. Test Oracle

The test oracle in the case of the mutation-based fuzzing testing was a *reference implementation*, OpenSSL. For the penetration testing, there oracle was *manual inspection*; whether a successful attack could occur or not.

##### 7. Specification

In the case of the mutation-based fuzzing the specification was *manually-determined* based on knowledge of what is or is not supposed to be done as part of the TLS protocol.

##### 8. Test Scope

The test scope was *system testing*, in this case, the entirety of nqsb-TLS.

##### 9. Exit Criteria

The exit criteria for the mutation-based fuzzing was *exhaustion* of the test set. The exit criteria for the penetration testing was *manual*, after a period of time the testing ended.

##### 10. Vulnerability to False Positives

Both forms of testing were *not vulnerable* to false positives.

##### 11. Form of Vulnerabilities

No vulnerabilities were discovered with nqsb-TLS. Of the faults discovered in OpenSSL, they of the form of *counterexamples*; comparison to what nqsb-TLS did given a particular input.

##### 12. Threat Assessment of Vulnerabilities

The threat assessment was performed *manually*.


*C. Finite-State Analysis of SSL 3.0*

The authors of this paper attempted to analyze the SSL protocol using a finite-state enumeration tool called Mur$\phi$. They completed the analysis by using a sequence of incremental approximations to the SSL 3.0 handshake that is then model-checked using Mur$\phi$. Though this methodology they discovered some anomalies in the session resumption protocol.

##### 1. Type

This paper uses a finite-state analysis tol and thus is a form of *static testing*; specifically, *model-checking*.

##### 2. Accessibility

The accessibility of artifacts was *white-box*; the authors had access to the protocol code and were specifically developing a "rational reconstruction" of the protocol.

##### 3. Exceptional Element

The finite state analysis tool Mur$\phi$ presents an *execution trace*, which in ths case is a sequence of states from the start state to a state exhibiting the problem; a counterexample to a guarantee that some invariant is held by the program.

##### 4. Injection Vector

Since this is static testing, no injection vector is present.

##### 5. Instrumentation

Since this is static testing, no instrumentation is present.

##### 6. Test Oracle

The test oracle in this case is a *specification* of invariants. 

##### 7. Specification

The form of the specification is a set of Boolean conditions that have to be true in every reachable state; essentially they bound what a "safe" state is. This had to be *manually determined* from the authors' knowledge of the SSL protocol.

##### 8. Test Scope

The test scope was *component testing*; only the SSL 3.0 handshake was under consideration. 

##### 9. Exit Criteria

The exit criteria was *coverage*, the Mur$\phi$ tool exhaustively tests all possible interleavings of protocol and intruder actions to ensure that a set of correctness conditions is satisfied in all cases.

##### 10. Vulnerability to False Positives

Although Mur$\phi$ was not vulnerable to false positives since it is a model-checker, it did uncover problems that were not necessarily direct threats to the security of SSL 3.0. 

##### 11. Form of Vulnerabilities

The form of the anomalies was *counterexamples* as is typical of model-checking.

##### 12. Threat Assessment of Vulnerabilities

The threat assessment of the anomalies was done *automatically* though the generation of a potential attack. 

*D. A Messy State of the Union*

The authors address the problem of designing a composite state machine that can multiplex between different protocol modes and then test several TLS implementations for state machine bugs.

##### 1. Type

The authors analyze the state machines of multiple open source TLS implementations using a combination of *dynamic testing*, specifically *generation-based fuzzing*, and manual source code analysis for the classification of errors, which is *static testing*, specifically *code-based analysis*.

##### 2. Accessibility

During the dynamic testing, the authors use FlexTLS to send arbitrary sequences of TLS messages to the implementations, and thus the dynamic testing is *black-box testing*. Since source code analysis is done duirng the static testing, it is *white-box testing*.

##### 3. Exceptional Element

The exceptional element in the case of the generation-based fuzzing is an *exceptional input* in the form of a sequence of valid TLS messages. For the code-based analysis there is no exceptional element.

##### 4. Injection Vector

For the dynamic testing, the injection vector is *simulated interaction*; FlexTLS simulates a clients connection to a server. There is no injection vector for the code-based analysis.

##### 5. Instrumentation

The instrumentation for the dynamic testing is *in-band*, the authors expect to get an error message from the implementation. There is no instrumentation for the static testing.

##### 6. Test Oracle

The test oracle for the dynamic testing is a *reference implementation*; the authors have a verified TLS state machine that is used to verify that a particular trace is valid or invalid (and thus that the implemetation should accept or reject it). The test oracle for the static testing is *manual inspection*.

##### 7. Specification

The specification for a particular trace is *automatically generated* in the sense that FlexTLS generates it and then the verified state machine accepts it. The specification for the code-based analysis is manually determined.

##### 8. Test Scope

The test scope for the dynamic testing is *system testing* since the implementations are viewed as black boxes. The test scope for the manual code analysis is also *system testing* since the manual analysis is done on the basis of the dynamic testing.

##### 9. Exit Criteria

The exit criteria for the dynamic testing is *exhaustion* of the test cases. The exit criteria for the manual code analysis is *manual*.

##### 10. Vulnerability to False Positives

For the dynamic testing, the authors attempt to reduce false positives by using verified messaging libraries, but they cannot guarantee that no false positives will be generated with this methodology. Thus, the dynamic testing is *vulnerable* to false positives. The code-based analysis is *not vulnerable* to false positives.

##### 11. Form of Vulnerabilities

The form of vulnerabilities is *exceptional traces*, errors from the SUT.

##### 12. Threat Assessment of Vulnerabilities

The threat assessment is done *manually*, though the manual code-based analysis.


*E. Systematic Fuzzing and Testing of TLS Libraries*

This paper presents TLS-Attacker, an open source framework for evaluating the security of TLS libraries through the creation of custom TLS message flows and the arbitrary modification of message contents. This framework is used to mount a two-stage fuzzing approach to evaluate TLS server behavior. 

##### 1. Type

The type of testing done is *dynamic testing*. The two stage approach is to first perform restricted *random fuzzing* specifically targeted at triggering Bleichenbacher's attack, padding oracle attacks, invalid curve attacks, and POODLE. In the second stage, three phases of attacks are performed, but they all use *mutation-based fuzzing* to send protocol flows with randomly modified variables.

##### 2. Accessibility

The accessibility is *black-box*, the source code of implementations is not inspected during testing.

##### 3. Exceptional Element

The exceptional element of both stages is an *exceptional input* of the form of a protocol flow.

##### 4. Injection Vector

The injection vector is a *simulated server*; the framework being used.

##### 5. Instrumentation

*Out-of-band* instrumentation is used. In order to detect buffer boundary violations or other memory corruptions, the authors utilize AddressSanitizer which monitors the program at runtime for memory errors. The authors also detect if an invalid protocol flow by using a TLS context analyzer. 

##### 6. Test Oracle

The test oracle in this case is *manual inspection* of the testing data.

##### 7. Specification

The specification is *manually determined* based on the authors' knowledge of the TLS protocol.

##### 8. Test Scope

The test scope is *system testing* since the SUT is an entire particular TLS implementation.

##### 9. Exit Criteria

The exit criteria is *manual*. A user of the testing framework configures the number of fuzzing attempts.

##### 10. Vulnerability to False Positives

This method is *vulnerable* to false positives.

##### 11. Form of Vulnerabilities

The form of vulnerabilities is *exceptional traces* both in terms of the output of AddressSanitizer and the TLS context analyzer.

##### 12. Threat Assessment of Vulnerabilities

The threat assessment had to be completed *manually*. Each potential vulnerability was analyzed by the authors.


*F. Protocol state fuzzing of TLS implementations*

The authors perform a systematic analysis of TLS implementations by using state machine learning to infer state machines from protocol implemetations, and then check those state machines manually to look for spurious behavior. If spurious behavior is found, the authors then manually investigate the implementations to look for vulnerabilities.

##### 1. Type

The type of testing is *static testing*, specifically it is *model-checking*, but the checking of the model is done manually.

##### 2. Accessibility

The accessibility of any code artifacts is *black-box*, the authors do not have access to the source code during testing, but then utilize it during the threat evaluation.

##### 3. Exceptional Element

The exceptional element is not applicable as the model checking is done manually.

##### 4. Injection Vector

There is no injection vector as the model checking is done manually.

##### 5. Instrumentation

There is no instrumentation as the model checking is done manually.

##### 6. Test Oracle

The test oracle in this case is *manual inspection* of the generated models.

##### 7. Specification

The specification is *manually determined*.

##### 8. Test Scope

The scope of the testing is *component testing*. Testing is done over models generated using message flows in the TLS handshake and with the heartbeat protocol.

##### 9. Exit Criteria

The exit criteria for the testing is *manual*.

##### 10. Vulnerability to False Positives

This methodology is *vulnerable* to false positives since any misbehavior must be inspected learning to some benign cases. 

##### 11. Form of Vulnerabilities

The form of vulnerabilities is the *manually-inspected behavior* of the generated model.

##### 12. Threat Assessment of Vulnerabilities

Threat assessment is done *manually*, if a spurious behavior is noticed in a generated model of a particular implementation, the authors then manually inspect the source code of that implementation.

*G. Testing Embedded TLS Implementations*

*H. Goanna—A Static Model Checker*


### IV. Result Classification


### V. Comparison of Methodologies and Results


### VI. [!] The Combined Approach


### VII. [!] Results with the Combined Approach


### VIII. [!] Related Works


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

