# Report for assignment 3

## Project

Name: Algorithms

URL: [https://github.com/williamfiset/Algorithms](https://github.com/williamfiset/Algorithms)

This repository a variety of algorithms and data structures implemented with Java.

## Onboarding experience

### Build and run

There was not much documentation about running tests, but the project uses gradle and `gradle test` ran all the tests and produced a nice web interface with the reports from the test. There is also doucmentation about how to run single algorithms using gradle, or only using a JDK.

Except for gradle, no additional tools or components were needed for running this software.

### Previous choice

We chose the repo `phishman3579/java-algorithms-implementation` before we noticed three other groups already chosen this repo, forcing us to change to another.

## Complexity

1. What are your results for eight complex functions?
   1. From the lizard tool:
      * LongestCommonPrefixArray::SuffixArray::construct (17)
      * SubstringVerificationSuffixArray::SuffixArray::construct (17)
      * LongestRepeatedSubstring::SuffixArray::construct (17)
      * GenericSegmentTree::GenericSegmentTree (24)
      * SuffixArrayFast::construct (17)
      * TreeIsomorphismWithBfs::encodeTree (17)
      * TspDynamicProgrammingIterative::solve (23)
      * LineSegmentLineSegmentIntersection::lineSegmentLineSegmentIntersection (20)

   2. Manual count
      * GenericSegmentTree::GenericSegmentTree (22.5)
      * TreeIsomorphismWithBfs::encodeTree (20)
      * TspDynamicProgrammingIterative::solve (18)
      * LineSegmentLineSegmentIntersection::lineSegmentLineSegmentIntersection (31)

3. Did all methods (tools vs. manual count) get the same result? Are the results clear?

The results were different when counted manually and by the tool. The results also differed between different manual counts. The main reason was probably that there are different interpretations as to what should be counted and and how. There are different models for counting to start with. We used [this](https://en.wikipedia.org/wiki/Cyclomatic_complexity), but even within this model interpretations about what for example classifies as a connected component, or whether if-statements with muliple conditions should be counted once or once per condition, differed. We also discussed how to count double edges, and different interpretations gave different answers for the count.

2. Are the functions just complex, or also long?

They are generally somewhat long, averaging at 30-40 lines of code. This is clearly above average length for functions in the code: most modules have functions with an average around 10 non-comment lines of code.

3. What is the purpose of the functions?

Many of the functions are constructors. They set up instances of different data structures.

4. Are exceptions taken into account in the given measurements?

No, we did not take this into account when counting manually. There are many if-statements only for throwing exceptions for different edge cases. This should not be counted when doing a manual count.

5. Is the documentation clear w.r.t. all the possible outcomes?

There is generally not much good comments in the code and it is lacking Javadoc. There are definitely outcomes that are not documented.

## Refactoring

Plan for refactoring complex code:

Estimated impact of refactoring (lower CC, but other drawbacks?).

Carried out refactoring (optional, P+):

git diff ...

## Coverage

### Tools

Document your experience in using a "new"/different coverage tool.

How well was the tool documented? Was it possible/easy/difficult to
integrate it with your build environment?

### Your own coverage tool

Show a patch (or link to a branch) that shows the instrumented code to
gather coverage measurements.

The patch is probably too long to be copied here, so please add
the git command that is used to obtain the patch instead:

git diff ...

What kinds of constructs does your tool support, and how accurate is
its output?

### Evaluation

1. How detailed is your coverage measurement?

2. What are the limitations of your own tool?

3. Are the results of your tool consistent with existing coverage tools?

## Coverage improvement

Show the comments that describe the requirements for the coverage.

Report of old coverage: [link]

Report of new coverage: [link]

Test cases added:

git diff ...

Number of test cases added: two per team member (P) or at least four (P+).

## Self-assessment: Way of working

Current state according to the Essence standard: ...

Was the self-assessment unanimous? Any doubts about certain items?

How have you improved so far?

Where is potential for improvement?

## Overall experience

What are your main take-aways from this project? What did you learn?

Is there something special you want to mention here?
