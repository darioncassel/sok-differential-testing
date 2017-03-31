1. Security testing verifies and validates software system requirements related to security properties like confidentiality, integrity, availability, authentication, authorization and nonrepudiation. 
2. Types: 
    1. model-based security testing is grounded on requirements and design models created during the analysis and design phase 
        1. model-based testing (MBT), manually selected algorithms automatically and systematically generate test cases from a set of models of the system under test or its environment 

    2. code-based testing and static analysis on source and bytecode created during development 
    3. penetration testing and dynamic analysis on running systems, either in a test or production environment 
    4. security regression testing performed during maintenance. 

3. According to the classic definition in software engineering [17], software testing consists of the dynamic verification that a program provides expected behaviors on a finite set of test cases, a so called test suite, suitably selected from the usually infinite execution domain. This dynamic notion of testing, so called dynamic testing, evaluates software by observing its execution [4]. 
    1. The executed system is called system under test (SUT). 

4. static testing checks software development artifact (e.g., requirements, design or code) without execution of these artifacts. 
    1. Types: 
        1. (manual) reviews 
        2. (automated) static analysis, often combined with dynamic testing, especially in the context of security 

5. After running a test case, the observed and intended behaviors of a SUT are compared with each other, which then results in a verdict. 
    1. pass (behaviors conform) 
    2. fail (behaviors don’t conform) 
    3. inconclusive (not known whether behaviors conform) [65]. 

6. test oracle is a mechanism for determining the verdict. 
    1. observed behavior may be checked against user or customer needs (commonly referred to as testing for validation) 
    2.  against a specification (testing for verification) 

7. A failure is an undesired behavior 
    1. A fault is the cause of the failure. 
        1. static defect in the software, usually caused by human error in the specification, design, or coding process. 
        2. a textual representation of what goes wrong in a behavioral description. It is the incorrect part of a behavioral description that needs to be replaced to get a correct description 
        3. vulnerability is a special type of fault. If the fault is related to security properties, it is called a vulnerability. A vulnerability is always related to one or more assets and 5 their corresponding security properties.  

8. Functional testing is concerned with assessing the functional behavior of an SUT, whereas  
9. Nonfunctional testing aims at assessing nonfunctional requirements with regard to quality characteristics like security, safety, reliability or performance. 
10. test scope describes the granularity of the SUT and can be classified into component, integration and system testing. 
    1. Component testing (also referred to as unit testing) checks  the smallest testable component (e.g., a class in an object-oriented implementation or a single electronic control unit) in isolation.  
    2. Integration testing combines components with each other and tests those as a subsystem, that is, not yet a complete system.  
    3. System testing checks the complete system, including all subsystems. 
    4. Regression testing is a selective retesting to verify that modifications have not caused side effects and that the SUT still complies with the specified requirements 

11. accessibility of test design artifacts 
    1. white-box testing, test cases are derived based on information about how the software has been designed or coded [17]. 
    2. blackbox testing, test cases rely only on the input/output behavior of the software 
        1. relevant for security testing where no or only basic information about the system under test is provided to mimic external attacks from hackers. 

12. A test plan includes the test objectives, test scope, and test methods as well as the resources, and schedule of intended test activities. It identifies, amongst others, features to be tested and exit criteria defining conditions for when to stop testing. Coverage criteria aligned with the tested feature types and the applied test design techniques are typical exit criteria 
13. Security properties: 
    1. Confidentiality is the assurance that information is not disclosed to unauthorized individuals, processes, or devices 
    2. Integrity is provided when data is unchanged from its source and has not been accidentally or maliciously modified, altered, or destroyed.  
    3. Availability guarantees timely, reliable access to data and information services for authorized users. 
    4. Authentication is a security measure designed to establish the validity of a transmission, message, or originator, or a means of verifying an individual’s authorization to receive specific categories of information.  
    5. Authorization provides access privileges granted to a user, program, or process. 
    6. Non-repudiation is the assurance that none of the partners taking part in a transaction can later deny of having participated. 

14. Security requirements can be formulated as positive requirements, explicitly defining the expected security functionality of a security mechanism, or as negative requirements, specifying what the application should not do [99]. 
15. Meaning of a vulnerability 
    1. (1) the responsible security mechanism is completely missing 
    2. (2) the security mechanism is in place but is implemented in a faulty way 

16. exploit is a concrete malicious input that makes use of the vulnerability in the system under test (SUT) and violates the property of an asset. 
17. For positive security requirements classical testing techniques can be applied applied, whereas for negative security requirements (a combination of) additional measures like risk analyses, penetration testing, or vulnerability knowledge bases are essential.  
18. Security functional testing validates whether the specified security requirements are implemented correctly, both in terms of security properties and security mechanisms. Security vulnerability testing addresses the identification of unintended system vulnerabilities 
    1. For security vulnerability testing approaches, Shahriar and Zulkernine [118], i.e., vulnerability coverage, source of test cases, test case generation method, testing level, test case granularity, tool automation as well as target applications. Tool automation is further refined into the criteria test case generation, oracle generation, and test case execution. The authors classify 20 informally collected approaches according to these criteria. 
    2. testing cannot show the absence of faults 

    1. Thompson [120] 

20. In the Security Development Lifecycle (SDL) [57] from Microsoft practices with strong interference with testing efforts are the following: 
    1. SDL Practice #10 (Implementation): Perform Static Analysis 
    2. SDL Practice #11 (Verification): Perform Dynamic Analysis 
    3. SDL Practice #12 (Verification): Perform Fuzz Testing 

21. Model-Based Testing 
    1. Its prospective benefits include early and explicit specification and review of system behavior, better test case documentation, the ability to automatically generate useful (regression) tests and control test coverage, improved maintenance of test cases as well as shorter schedules and lower costs [114]. 
    2. Process 
        1. (1) A model of the SUT and/or its environment is built from informal requirements, existing specification documents, or a SUT. 
            1. The resulting model of the SUT dedicated to test generation is often called test model. 

        2.  (2) If they are executable, one execution trace of such a model acts as a test case: input and expected output for an SUT. 
            1. Because there are usually infinitely many and infinitely long execution traces, models can therefore be used to generate an infinite number of tests. To cut down their number and length, test selection criteria are applied. These guide the generation of tests. 

        3. (3) Once the test model and the test selection criteria are defined, a set of test cases is generated from the test model as determined by the chosen test selection criteria.  
            1. Test generation is typically performed automatically.  
            2. The generated test cases are traces of the model and thus in general at a higher level than the events or actions of an SUT 
            3. Therefore, the generated test cases are further refined to a more concrete level or adapted to the SUT to support their automated execution. 

    3. “Good” test cases 
        1. Detect potential, rather than actual, defects with good cost effectiveness [104]. 
        2. Potential defects need to be described by defect hypotheses. In order to turn these hypotheses into operational adequacy criteria [145], they need to be captured by some form of explicit defect model [93, 105, 104]. 
            1. One form of defect is a fault, understood as the root cause of an incorrect system state (error) or incorrect system output (failure). As we show below, vulnerabilities can be understood as faults. 

22. Models of threats: 
    1. Models of an attacker encode an attacker’s behavior: the data they need, the different steps they take, the way they craft exploits.  
    2. Models of vulnerabilities explicitly encode weaknesses in a system or a model of the system. 
    3. Properties describe desired characteristics of a model, or an implementation, and they include confidentiality, availability, integrity, and so on. 

23. Security Mechanisms: 
    1. Mechanisms are components of a system and can always be syntactically recognized: there is a piece of code (the mechanism) that is supposed to protect the asset; or there is no such piece of code. 
    2. vulnerability is defined as the absence of a correctly functioning mechanism. Can be either, or both: 
        1. (1) that there is no mechanism at all (e.g., no input sanitization takes place which can lead to buffer overflows or SQL Injections) 
        2. (2) that the mechanism does not work correctly, i.e., is partially or incorrectly implemented, for instance, if an access control policy is faulty. 

24. Security Testing: 
    1. (1) to test if specific security properties of an asset can be violated (properties and property models);  
    2. (2) to test the functionality of a mechanism (attacker models); and  
    3. (3) to directly try to exploit a vulnerability (vulnerability models) 

25.  Classification proposed by Schieferdecker et al. [116] 
    1. architectural and functional models 
        1. concerned with security requirements and their implementation. 

    2. threat, fault and risk models 
        1.  focus on what can go wrong, and concentrate on causes and consequences of system failures, weaknesses or vulnerabilities 

    3. weakness and vulnerability models 
        1.  describe weaknesses or vulnerabilities by themselves 

26. Static Application Security Testing (SAST) [26] 
    1.  SAST tool mainly employs two different types of analysis: 
        1. Syntactic checks such as calling insecure API functions or using insecure configuration options. 
        2. Semantic checks that require an understanding of the program semantics such as the data flow or control flow of a program. 

    2. Output of a SAST tool is a list of potential security vulnerabilities. Thus, for each finding, an human expert is necessary to decide: 
        1. If the finding represents a vulnerability 
        2. If the finding cannot be exploited by an attacker (false positive) 

    3. If an SAST tool does not report security issues, this can have two reasons: 
        1. Source code is secure 
        2. Source code has security vulnerability but due to limitations of the tool, the tool does not report a problem (false negative). 

27. Black-box security testing does not require access to the source code or other development artifacts of the application under test. Instead, the security test is conducted via interaction with the running software 
    1. Fuzzing or fuzz testing is a dynamic testing technique that is based on the idea of feeding random data to a program “until it crashes.” 
        1. Random fuzzing is the simplest and oldest fuzz testing technique: a stream of random input data is, in a black-box scenario, send to the program under test. 

    2. Mutation-based fuzzing is one type of fuzzing in which the fuzzer has some knowledge about the input format of the program under test:  
        1. Based on existing data samples, a mutation-based fuzzing tools generated new variants (mutants) based on a heuristics, that it uses for fuzzing 

    3. Generation-based fuzzing used a model (of the input data or the vulnerabilities) for generating test data from this model or specification.  
        1. Compared to pure random-based fuzzing, generation-based fuzzing achieves usually a higher coverage of the program under test, in particular if the expected input format is rather complex [138, 144]. 

    4. Concolic testing [12, 1] 
        1. Combines symbolic execution (a static source code analysis technique) and dynamic testing.