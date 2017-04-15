## SoK: SSL/TLS Security Protocol Testing and Verification

Darion Cassel<sup>1</sup>, David Evans<sup>1</sup>

<sup>1</sup>University of Virginia, Charlottesville, Virginia, USA
<hr>

### Abstract

TLS/SSL implementations have been shown to be have a slew of vulnerabilities that can lead to violation of its fundamental security properties. We classify current approaches to the testing of TLS implementations through the use of established taxonomies developed in the field of software engineering. Through the lens of this categorization, we assert that security protocol implementation vulnerabilities should be treated differently from traditional software faults because they are a violation of *two levels* of specification. On one level, they are an exceptional state of some protocol implementation’s state machine. On another level, they are a violation of a protocol model with the consequence of a violation of some security property that model intends to guarantee. No current approaches work on both levels but we believe that in order to discover a security fault and understand the implications of that fault without human intervention an approach must be able to combine information from both levels of testing. We present a cohesive workflow that combines two approaches in order to discover faults and classify the semantic threat of those faults concurrently.

### Methodology
1. [x] Standardization of a framework for approach classification
2. [x] Classification of current approaches within this framework
3. [x] Standardization of a framework for result classification
4. [x] Classification of results within this framework
5. [ ] Matrix of approaches x results
6. [ ] Presentation of workflow combining two or more approaches
7. [ ] (Time-permitting) Conterfactual testing of TLS as a baseline evaluation standard to enhance comparison

### Terminology and Approach Classification:



Information from [Survey of Security Testing](https://blogs.uni-paderborn.de/sse/files/2015/08/book_chapter_sectesting.pdf?file=2015/08/book_chapter_sectesting.pdf)

