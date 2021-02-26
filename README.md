# Report for assignment 3

## Project

Name: Algorithms

URL: [https://github.com/williamfiset/Algorithms](https://github.com/williamfiset/Algorithms)

This repository a variety of algorithms and data structures implemented with Java.

### P+ plan

We went for the following P+ criteria:

- Each group member writes at least four new or enhanced unit tests
- You use your issue tracker and systematic commit messages to manage your project. [link](https://github.com/soffgrupp6/assignment3)
- You carry out some of Task 3: you refactor at least two of the functions (per group) with high cyclomatic complexity to reduce it by at least 35%.
- Something extraordinary

#### Extraordinary

TODO

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

The results were different when counted manually and by the tool. The results also differed between different manual counts. The main reason was probably that there are different interpretations as to what should be counted and and how. There are different models for counting to start with. We used [this](https://en.wikipedia.org/wiki/Cyclomatic_complexity), but even within this model interpretations about what for example classifies as a connected component, or whether if-statements with multiple conditions should be counted once or once per condition, differed. We also discussed how to count double edges, and different interpretations gave different answers for the count.

2. Are the functions just complex, or also long?

They are generally somewhat long, averaging at 30-40 lines of code. This is clearly above average length for functions in the code: most modules have functions with an average around 10 non-comment lines of code.

3. What is the purpose of the functions?

Many of the functions are constructors. They set up instances of different data structures.

- The lineSegmentLineSegmentIntersection function takes two lines segments and calculates the intersection of them and then returns the intersection. It has many different cases for how line segments intersect each other.
- The TspDynamicProgrammingIterative::solve function is as the name suggests a TSP instance solver.
- GenericSegmentTree is a class that implements a segment tree where the constructor sets up different segment combination functions depending on the input
- TreeIsomorphismWithBfs::encodeTree takes a tree and encodes it as a string in a way that represents all isomorphic trees the same way.

4. Are exceptions taken into account in the given measurements?

No, we did not take this into account when counting manually. There are many if-statements only for throwing exceptions for different edge cases. This should not be counted when doing a manual count.

5. Is the documentation clear w.r.t. all the possible outcomes?

There is generally not much good comments in the code and it is lacking Javadoc. There are definitely outcomes that are not documented.

## Coverage

### Tools

We used Jacoco and it was only a matter of adding a few lines to the Gradle build file to make it work and generate a HTML website with coverage measurement results. It was very easy to integrate and required no installation if you had the right version of Gradle and Java running on your system.

One problem was, however, that it did not work for all versions of Java. If you had a version that was too new, it was very hard to get it to work.

### Your own coverage tool

In [this](https://github.com/soffgrupp6/Algorithms/tree/issue/7) branch, our coverage measurement tool is implemented for the methods lineSegmentLineSegmentIntersection, TspDynamicProgrammingIterative::solve, GenericSegmentTree, GenericSegmentTree2 and TreeIsomorphismWithBfs::encodeTree. It can be run by running the tests for the methods, and its results can be found in Algorithms/build/reports/tests/test/index.html.

The tool is implemented using a static array with booleans. Each boolean represents a branch, and when that branch is hit by a test, its corresponding boolean is set to true.

The tool supports if-statements, for- and while-loops but mostly does not consider conditionals like || and && and their execution. Our ad-hoc method proved to be quite close to the automatically generated coverage report but it differed slightly under different circumstances.

One example of a patch using the "tool" can be found below (we included diffs to make it easier to see the changes we introduced without navigating all the different branches). The ratio of coverage is written to stdout after every test run.

```diff
diff --git a/src/main/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersection.java b/src/main/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersection.java
index d8b6768..b3b359d 100644
--- a/src/main/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersection.java
+++ b/src/main/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersection.java
@@ -39,13 +40,20 @@ public class LineSegmentLineSegmentIntersection {
   // Finds the intersection point(s) of two line segments. Unlike regular line
   // segments, segments which are points (x1 = x2 and y1 = y2) are allowed.
   public static Pt[] lineSegmentLineSegmentIntersection(Pt p1, Pt p2, Pt p3, Pt p4) {
-
+    array[1] = true;
     // No intersection.
-    if (!segmentsIntersect(p1, p2, p3, p4)) return new Pt[] {};
-
+    if (!segmentsIntersect(p1, p2, p3, p4)) {
+      array[2] = true;
+      return new Pt[] {};
+    }
+    array[3] = true;
     // Both segments are a single point.
-    if (p1.equals(p2) && p2.equals(p3) && p3.equals(p4)) return new Pt[] {p1};
+    if (p1.equals(p2) && p2.equals(p3) && p3.equals(p4)) {
+      array[4] = true;
+      return new Pt[] {p1};
+    }

+    array[5] = true;
     List<Pt> endpoints = getCommonEndpoints(p1, p2, p3, p4);
     int n = endpoints.size();

@@ -53,22 +61,41 @@ public class LineSegmentLineSegmentIntersection {
     // NOTE: checking only n == 1 is insufficient to return early
     // because the solution might be a sub segment.
     boolean singleton = p1.equals(p2) || p3.equals(p4);
-    if (n == 1 && singleton) return new Pt[] {endpoints.get(0)};
+    if (n == 1 && singleton) {
+      array[6] = true;
+      return new Pt[] {endpoints.get(0)};
+    }
+    array[7] = true;
+

     // Segments are equal.
-    if (n == 2) return new Pt[] {endpoints.get(0), endpoints.get(1)};
+    if (n == 2) {
+      array[8] = true;
+      return new Pt[] {endpoints.get(0), endpoints.get(1)};
+    }
+    array[9] = true;
+

     boolean collinearSegments = (orientation(p1, p2, p3) == 0) && (orientation(p1, p2, p4) == 0);

     // The intersection will be a sub-segment of the two
     // segments since they overlap each other.
     if (collinearSegments) {
+      array[10] = true;

       // Segment #2 is enclosed in segment #1
-      if (pointOnLine(p1, p2, p3) && pointOnLine(p1, p2, p4)) return new Pt[] {p3, p4};
+      if (pointOnLine(p1, p2, p3) && pointOnLine(p1, p2, p4)) {
+        array[11] = true;
+        return new Pt[] {p3, p4};
+      }

       // Segment #1 is enclosed in segment #2
-      if (pointOnLine(p3, p4, p1) && pointOnLine(p3, p4, p2)) return new Pt[] {p1, p2};
+      if (pointOnLine(p3, p4, p1) && pointOnLine(p3, p4, p2)) {
+        array[12] = true;
+        return new Pt[] {p1, p2};
+      }
+
+      array[13] = true;

       // The subsegment is part of segment #1 and part of segment #2.
       // Find the middle points which correspond to this segment.
@@ -76,26 +103,39 @@ public class LineSegmentLineSegmentIntersection {
       Pt midPoint2 = pointOnLine(p3, p4, p1) ? p1 : p2;

       // There is actually only one middle point!
-      if (midPoint1.equals(midPoint2)) return new Pt[] {midPoint1};
-
+      if (midPoint1.equals(midPoint2)) {
+        array[14] = true;
+        return new Pt[] {midPoint1};
+      }
+      array[15] = true;
       return new Pt[] {midPoint1, midPoint2};
     }

+    array[16] = true;
+
     /* Beyond this point there is a unique intersection point. */

     // Segment #1 is a vertical line.
     if (abs(p1.x - p2.x) < EPS) {
+      array[17] = true;
+
       double m = (p4.y - p3.y) / (p4.x - p3.x);
       double b = p3.y - m * p3.x;
       return new Pt[] {new Pt(p1.x, m * p1.x + b)};
     }
+    array[18] = true;
+

     // Segment #2 is a vertical line.
     if (abs(p3.x - p4.x) < EPS) {
+      array[19] = true;
+
       double m = (p2.y - p1.y) / (p2.x - p1.x);
       double b = p1.y - m * p1.x;
       return new Pt[] {new Pt(p3.x, m * p3.x + b)};
     }
+    array[0] = true;
+

     double m1 = (p2.y - p1.y) / (p2.x - p1.x);
     double m2 = (p4.y - p3.y) / (p4.x - p3.x);
diff --git a/src/test/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersectionTest.java b/src/test/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersectionTest.java
new file mode 100644
index 0000000..e6caa68
--- /dev/null
+++ b/src/test/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersectionTest.java
@@ -0,0 +1,26 @@

+
+  @After
+  public void after() {
+    int trues = 0;
+
+    for(boolean elem : LineSegmentLineSegmentIntersection.array) {
+      if(elem == true) trues++;
+    }
+
+    System.out.println(((float) trues / LineSegmentLineSegmentIntersection.array.length));
+  }
+}
```

### Evaluation

1. How detailed is your coverage measurement?
It is quite detailed but differed slightly from the automatic tools. Our functions had approximately 15-25 branches that the tool considered.

2. What are the limitations of your own tool?
One major limitation is that you have to manually add a line of code for each branch that you would like to have included in the test. Some other limitations are that it does not support conditionals like || and &&, or ternary operators. These would have to be broken up into several statements.

3. Are the results of your tool consistent with existing coverage tools?
Yes, it proved to be relatively consistent with Jacoco.

## Coverage improvement

Our improvements are made in [this](https://github.com/soffgrupp6/Algorithms/tree/improvement) branch. There were two functions that got approximately 100% coverage so we added tests for one other function with some coverage and also created a test suite for a function with no coverage. Tests were added to hit the requirements that were not covered by other tests.

The before and after results can be seen in the table below:

| Function      | Cov before (manual) | Cov before (Jacoco) | Cov after (manual) | Cov after (Jacoco) | No. of tests |
| -------------                                                      |--------------:| -----:| ----:|-----:|---:|
| GenericSegmentTree::GenericSegmentTree                             | 0,54 | 0,54 | 0,71 | 0,73 | 4 |
| TreeIsomorphismWithBfs::encodeTree                                  |   0,93  |  0,93 | 1 | 0,96 | 1 |
| GenericSegmentTree2::GenericSegmentTree2                              | 0,76  |   0,75 | 0,94 | 0,86 | 3 |
| TspDynamicProgrammingIterative::solve                                  | 0,94 | 0,97 | 1 | 1 | 1 |
| LineSegmentLineSegmentIntersection::lineSegmentLineSegmentIntersection | 0,5 | 0,35 | 0,75 | 0,69 | 4 |
| IsPrime::IsPrime (added because of 100% cov on other)                 | 0 | 0 | 0,8 | 0,87 | 4 |


Number of test cases added: at least four per team member (P+).
See the diff below for tests added:

```diff
diff --git a/src/test/java/com/williamfiset/algorithms/datastructures/segmenttree/GenericSegmentTree2Test.java b/src/test/java/com/williamfiset/algorithms/datastructures/segmenttree/GenericSegmentTree2Test.java
index 157bb6d..3a4db6e 100644
--- a/src/test/java/com/williamfiset/algorithms/datastructures/segmenttree/GenericSegmentTree2Test.java
+++ b/src/test/java/com/williamfiset/algorithms/datastructures/segmenttree/GenericSegmentTree2Test.java
@@ -8,6 +8,7 @@ import static com.google.common.truth.Truth.assertThat;

 import com.williamfiset.algorithms.utils.TestUtils;
 import org.junit.Before;
+import org.junit.After;
 import org.junit.Test;

 public class GenericSegmentTree2Test {
@@ -452,6 +453,51 @@ public class GenericSegmentTree2Test {
     }
   }

+  //Tests that exception is thrown when values = null
+  @Test(expected = IllegalArgumentException.class)
+  public void testNullValues() {
+    long[] values = null;
+    GenericSegmentTree2 st =
+        new GenericSegmentTree2(
+            values,
+            GenericSegmentTree2.SegmentCombinationFn.SUM,
+            GenericSegmentTree2.RangeUpdateFn.ADDITION);
+  }
+
+  //Tests that exception is thrown when SegmentCombinationFn = null
+  @Test(expected = IllegalArgumentException.class)
+  public void testNullSegmentCombinationFn() {
+    long[] values = {2, 1, 3, 4, -1};
+    GenericSegmentTree2 st =
+        new GenericSegmentTree2(
+            values,
+            null,
+            GenericSegmentTree2.RangeUpdateFn.ADDITION);
+  }
+
+  //Tests that exception is thrown when RangeUpdateFunction = null
+  @Test(expected = IllegalArgumentException.class)
+  public void testNullRangeUpdateFunction() {
+    long[] values = {2, 1, 3, 4, -1};
+    GenericSegmentTree2 st =
+        new GenericSegmentTree2(
+            values,
+            GenericSegmentTree2.SegmentCombinationFn.SUM,
+            null);
+  }
diff --git a/src/test/java/com/williamfiset/algorithms/datastructures/segmenttree/GenericSegmentTreeTest.java b/src/test/java/com/williamfiset/algorithms/datastructures/segmenttree/GenericSegmentTreeTest.java
index 163dc99..b134bcf 100644
--- a/src/test/java/com/williamfiset/algorithms/datastructures/segmenttree/GenericSegmentTreeTest.java
+++ b/src/test/java/com/williamfiset/algorithms/datastructures/segmenttree/GenericSegmentTreeTest.java
@@ -413,6 +413,46 @@ public class GenericSegmentTreeTest {

 // ------------------------------------------------MY TESTS BEGIN HERE----------------------------------------------------

+  @Test
+  public void testSegMinRangMul() {
+    long[] values = {0,5,3,0,1};
+    GenericSegmentTree st = new GenericSegmentTree(
+      values,
+      GenericSegmentTree.SegmentCombinationFn.MIN,
+      GenericSegmentTree.RangeUpdateFn.MULTIPLICATION);
+    assertThat(st.rangeQuery1(0, 4) == 0);
+  }
+
+  @Test(expected = IllegalArgumentException.class)
+  public void testNoRangeFunction() {
+    long[] values = {1,1,1,1};
+    GenericSegmentTree st = new GenericSegmentTree(
+      values,
+      GenericSegmentTree.SegmentCombinationFn.SUM,
+      null);
+  }
+
+  @Test
+  public void testSegProRangAdd() {
+    long[] values = {0,5,3,0,1};
+    GenericSegmentTree st = new GenericSegmentTree(
+      values,
+      GenericSegmentTree.SegmentCombinationFn.PRODUCT,
+      GenericSegmentTree.RangeUpdateFn.ADDITION);
+    assertThat(st.rangeQuery1(0, 4) == 8);
+  }
+
+  @Test
+  public void testSegProRangMul() {
+    long[] values = {1,5,3,2,1};
+    GenericSegmentTree st = new GenericSegmentTree(
+      values,
+      GenericSegmentTree.SegmentCombinationFn.PRODUCT,
+      GenericSegmentTree.RangeUpdateFn.MULTIPLICATION);
+    assertThat(st.rangeQuery1(0, 4) == 30);
+  }
+
+
   @After
   public void after() {
     int trues = 0;
diff --git a/src/test/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersectionTest.java b/src/test/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersectionTest.java
index e6caa68..e693715 100644
--- a/src/test/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersectionTest.java
+++ b/src/test/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersectionTest.java
@@ -5,6 +5,7 @@ import static com.google.common.truth.Truth.assertThat;
 import com.google.common.collect.ImmutableList;
 import java.awt.geom.*;
 import org.junit.*;
+import com.williamfiset.algorithms.geometry.LineSegmentLineSegmentIntersection.Pt;

 public class LineSegmentLineSegmentIntersectionTest {

@@ -13,6 +14,50 @@ public class LineSegmentLineSegmentIntersectionTest {
     LineSegmentLineSegmentIntersection.main(new String[]{""});
   }

+  @Test
+  public void testSegment2enclosedInSegment1() {
+    Pt p1 = new Pt(0,2);
+    Pt p2 = new Pt(0,6);
+    Pt p3 = new Pt(0,0);
+    Pt p4 = new Pt(0,9);
+
+    Pt[] pts = LineSegmentLineSegmentIntersection.lineSegmentLineSegmentIntersection(p1, p2, p3, p4);
+    assertThat(pts[0].equals(p1)).isTrue();
+    assertThat(pts[1].equals(p2)).isTrue();
+  }
+  @Test
+  public void testNoIntersection() {
+    Pt p1 = new Pt(0,0);
+    Pt p2 = new Pt(0,6);
+    Pt p3 = new Pt(1,0);
+    Pt p4 = new Pt(2,9);
+
+    Pt[] pts = LineSegmentLineSegmentIntersection.lineSegmentLineSegmentIntersection(p1, p2, p3, p4);
+    assertThat(pts).isEmpty();
+  }
+
+  @Test
+  public void testAllEqual() {
+    Pt p1 = new Pt(0,0);
+    Pt p2 = new Pt(0,0);
+    Pt p3 = new Pt(0,0);
+    Pt p4 = new Pt(0,0);
+
+    Pt[] pts = LineSegmentLineSegmentIntersection.lineSegmentLineSegmentIntersection(p1, p2, p3, p4);
+    assertThat(pts[0].equals(p1)).isTrue();
+  }
+
+  @Test
+  public void testContinuousLineConnectedInOnePoint() {
+    Pt p1 = new Pt(0,0);
+    Pt p2 = new Pt(0,10);
+    Pt p3 = new Pt(0,10);
+    Pt p4 = new Pt(0,20);
+
+    Pt[] pts = LineSegmentLineSegmentIntersection.lineSegmentLineSegmentIntersection(p1, p2, p3, p4);
+    assertThat(pts[0].equals(p2)).isTrue();
+  }
+
   @After
   public void after() {
     int trues = 0;
diff --git a/src/test/java/com/williamfiset/algorithms/graphtheory/TravelingSalesmanProblemTest.java b/src/test/java/com/williamfiset/algorithms/graphtheory/TravelingSalesmanProblemTest.java
index 262b286..b695c27 100644
--- a/src/test/java/com/williamfiset/algorithms/graphtheory/TravelingSalesmanProblemTest.java
+++ b/src/test/java/com/williamfiset/algorithms/graphtheory/TravelingSalesmanProblemTest.java
@@ -194,6 +194,18 @@ public class TravelingSalesmanProblemTest {
     }
   }

+  @Test
+  public void testRunningTwiceToCheckRunSolverIsTrue() {
+    double[][] dist = new double[5][5];
+    randomFillDistMatrix(dist);
+    TspDynamicProgrammingIterative solver = new TspDynamicProgrammingIterative(dist);
+
+    solver.solve();
+    solver.solve();
+
+    assertThat(solver.ranSolver).isTrue();
+  }
+
   @After
   public void after() {
     int trues = 0;
diff --git a/src/test/java/com/williamfiset/algorithms/graphtheory/treealgorithms/TreeIsomorphismWithBfsTest.java b/src/test/java/com/williamfiset/algorithms/graphtheory/treealgorithms/TreeIsomorphismWithBfsTest.java
index e192e0b..6a16df3 100644
--- a/src/test/java/com/williamfiset/algorithms/graphtheory/treealgorithms/TreeIsomorphismWithBfsTest.java
+++ b/src/test/java/com/williamfiset/algorithms/graphtheory/treealgorithms/TreeIsomorphismWithBfsTest.java
@@ -126,6 +126,15 @@ public class TreeIsomorphismWithBfsTest {
     assertThat(treeEncoding).isEqualTo(expectedEncoding);
   }

+  @Test
+  public void testNullTree() {
+    // Test that empty tree returns ""
+    List<List<Integer>> tree = null;
+    String treeEncoding = encodeTree(tree);
+    String expectedEncoding = "";
+    assertThat(treeEncoding).isEqualTo(expectedEncoding);
+  }
+
   @Test
   public void t() {
     List<List<Integer>> tree = createEmptyTree(10);
diff --git a/src/test/java/com/williamfiset/algorithms/math/IsPrimeTest.java b/src/test/java/com/williamfiset/algorithms/math/IsPrimeTest.java
index 7eaf200..e1cbc53 100644
--- a/src/test/java/com/williamfiset/algorithms/math/IsPrimeTest.java
+++ b/src/test/java/com/williamfiset/algorithms/math/IsPrimeTest.java
@@ -14,7 +14,46 @@ public class IsPrimeTest {
     public void test1() {
         assertThat(true).isTrue();
     }
-
+
+    @Test
+    public void testForMultiplePrimes() {
+        int[] primes = {7, 3, 5, 11, 19};
+
+        for(int i = 0; i < primes.length; i++)
+            assertThat(IsPrime.isPrime(primes[i])).isTrue();
+    }
+
+    @Test
+    public void testForMultipleNonPrimes() {
+        int[] primes = {16, 28, 100, 102, 786};
+
+        for(int i = 0; i < primes.length; i++)
+            assertThat(IsPrime.isPrime(primes[i])).isFalse();
+    }
+
+    @Test
+    public void testForBigNumbers() {
+        int[] primes = {123123423, 234232, 634626, 8723432, 74826, 1234567, 73458934};
+
+        for(int i = 0; i < primes.length; i++)
+            assertThat(IsPrime.isPrime(primes[i])).isFalse();
+    }
+
+    @Test
+    public void testForNumbersLessThan2() {
+        int[] primes = {1, 0};
+
+        for(int i = 0; i < primes.length; i++)
+            assertThat(IsPrime.isPrime(primes[i])).isFalse();
+    }
+
+    @Test
+    public void testForNumbersDivisbleBy2And3() {
+        int[] primes = {4, 6, 8, 12};
+
+        for(int i = 0; i < primes.length; i++)
+            assertThat(IsPrime.isPrime(primes[i])).isFalse();
+    }

     @After
     public void after() {
```

## Refactoring

### Plan for refactoring complex code:

#### LineSegmentLineSegmentIntersection
The plan for refactoring the LineSegment function is to *move the handling of collinear line segments* to a helper function, thus reducing the complexity of the function. The collinear line segments are calculated in one if-statement block and is very suitable for refactoring. These changes might, however, make the code less easy to follow.

#### GenericSegmentTree
The plan for refactoring GenericSegmentTree is to move the part of the code setting SegmentCombinationFn and RangeUpdateFn to a helper function to get separation and a better overview of what the function really does. This fits quite well since that part is the very heavily branched part consisting of many if statements.

#### encodeTree
The plan for refactoring encodeTree is to move out several smaller blocks to helper methods: the bfs for finding all leaf nodes could be written in a separate findLeafNodes() method, and updating parent nodes could be written in a updateParentLabel() method. This would also improve readability, since these methods have a clear and distinct functionality and could be broken out without being confusing. This would, however, lead to many methods that might only be used few times in the class.

### Carried out refactoring:

We carried out refactoring for the LineSegment function and the GenericSegmentTree constructor. The refactoring was made in [this](https://github.com/soffgrupp6/Algorithms/tree/refactor) branch. In the *LineSegment* function, the complexity (from the lizard tool) was **20** and after the refactor it went down to **13**. The *GenericSegmentTree* complexity improved from **24** to **4**. The part that had many different branches and cases was moved to its own appropriately named helper function, thus reducing the complexity by a very high amount. There was, however, obviously still much complexity in the helper function but there was not much use separating the function further without making it much harder to follow.

See changes in the diffs below:

```diff
diff --git a/src/main/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersection.java b/src/main/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersection.java
index d8b6768..2fce277 100644
--- a/src/main/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersection.java
+++ b/src/main/java/com/williamfiset/algorithms/geometry/LineSegmentLineSegmentIntersection.java
@@ -36,6 +36,24 @@ public class LineSegmentLineSegmentIntersection {
     }
   }

+  private static Pt[] handleCollinearPoints(Pt p1, Pt p2, Pt p3, Pt p4) {
+    // Segment #2 is enclosed in segment #1
+    if (pointOnLine(p1, p2, p3) && pointOnLine(p1, p2, p4)) return new Pt[] {p3, p4};
+
+    // Segment #1 is enclosed in segment #2
+    if (pointOnLine(p3, p4, p1) && pointOnLine(p3, p4, p2)) return new Pt[] {p1, p2};
+
+    // The subsegment is part of segment #1 and part of segment #2.
+    // Find the middle points which correspond to this segment.
+    Pt midPoint1 = pointOnLine(p1, p2, p3) ? p3 : p4;
+    Pt midPoint2 = pointOnLine(p3, p4, p1) ? p1 : p2;
+
+    // There is actually only one middle point!
+    if (midPoint1.equals(midPoint2)) return new Pt[] {midPoint1};
+
+    return new Pt[] {midPoint1, midPoint2};
+  }
+
   // Finds the intersection point(s) of two line segments. Unlike regular line
   // segments, segments which are points (x1 = x2 and y1 = y2) are allowed.
   public static Pt[] lineSegmentLineSegmentIntersection(Pt p1, Pt p2, Pt p3, Pt p4) {
@@ -63,22 +81,7 @@ public class LineSegmentLineSegmentIntersection {
     // The intersection will be a sub-segment of the two
     // segments since they overlap each other.
     if (collinearSegments) {
-
-      // Segment #2 is enclosed in segment #1
-      if (pointOnLine(p1, p2, p3) && pointOnLine(p1, p2, p4)) return new Pt[] {p3, p4};
-
-      // Segment #1 is enclosed in segment #2
-      if (pointOnLine(p3, p4, p1) && pointOnLine(p3, p4, p2)) return new Pt[] {p1, p2};
-
-      // The subsegment is part of segment #1 and part of segment #2.
-      // Find the middle points which correspond to this segment.
-      Pt midPoint1 = pointOnLine(p1, p2, p3) ? p3 : p4;
-      Pt midPoint2 = pointOnLine(p3, p4, p1) ? p1 : p2;
-
-      // There is actually only one middle point!
-      if (midPoint1.equals(midPoint2)) return new Pt[] {midPoint1};
-
-      return new Pt[] {midPoint1, midPoint2};
+      return handleCollinearPoints(p1, p2, p3, p4);
   }
```

```diff
diff --git a/src/main/java/com/williamfiset/algorithms/datastructures/segmenttree/GenericSegmentTree.java b/src/main/java/com/williamfiset/algorithms/datastructures/segmenttree/GenericSegmentTree.java
index 98a1d5f..c0aab45 100644
--- a/src/main/java/com/williamfiset/algorithms/datastructures/segmenttree/GenericSegmentTree.java
+++ b/src/main/java/com/williamfiset/algorithms/datastructures/segmenttree/GenericSegmentTree.java
@@ -162,6 +162,74 @@ public class GenericSegmentTree {
   private Ruf productQueryAssignUpdate = (b, tl, tr, d) -> d;
   private Ruf lproductQueryAssignUpdate = (b, tl, tr, d) -> d;

+  private void SetCombinationFnAndRuf(SegmentCombinationFn scfn, RangeUpdateFn rufn) {
+        // Select the specified combination function
+        if (scfn == SegmentCombinationFn.SUM) {
+          combinationFn = sumCombinationFn;
+          if (rufn == RangeUpdateFn.ADDITION) {
+            ruf = sumQuerySumUpdate;
+            lruf = lsumQuerySumUpdate;
+          } else if (rufn == RangeUpdateFn.ASSIGN) {
+            ruf = sumQueryAssignUpdate;
+            lruf = lsumQueryAssignUpdate;
+          } else if (rufn == RangeUpdateFn.MULTIPLICATION) {
+            ruf = sumQueryMulUpdate;
+            lruf = lsumQueryMulUpdate;
+          }
+        } else if (scfn == SegmentCombinationFn.MIN) {
+          combinationFn = minCombinationFn;
+          if (rufn == RangeUpdateFn.ADDITION) {
+            ruf = minQuerySumUpdate;
+            lruf = lminQuerySumUpdate;
+          } else if (rufn == RangeUpdateFn.ASSIGN) {
+            ruf = minQueryAssignUpdate;
+            lruf = lminQueryAssignUpdate;
+          } else if (rufn == RangeUpdateFn.MULTIPLICATION) {
+            ruf = minQueryMulUpdate;
+            lruf = lminQueryMulUpdate;
+          }
+        } else if (scfn == SegmentCombinationFn.MAX) {
+          combinationFn = maxCombinationFn;
+          if (rufn == RangeUpdateFn.ADDITION) {
+            ruf = maxQuerySumUpdate;
+            lruf = lmaxQuerySumUpdate;
+          } else if (rufn == RangeUpdateFn.ASSIGN) {
+            ruf = maxQueryAssignUpdate;
+            lruf = lmaxQueryAssignUpdate;
+          } else if (rufn == RangeUpdateFn.MULTIPLICATION) {
+            ruf = maxQueryMulUpdate;
+            lruf = lmaxQueryMulUpdate;
+          }
+        } else if (scfn == SegmentCombinationFn.GCD) {
+          combinationFn = gcdCombinationFn;
+          if (rufn == RangeUpdateFn.ADDITION) {
+            ruf = gcdQuerySumUpdate;
+            lruf = lgcdQuerySumUpdate;
+          } else if (rufn == RangeUpdateFn.ASSIGN) {
+            ruf = gcdQueryAssignUpdate;
+            lruf = lgcdQueryAssignUpdate;
+          } else if (rufn == RangeUpdateFn.MULTIPLICATION) {
+            ruf = gcdQueryMulUpdate;
+            lruf = lgcdQueryMulUpdate;
+          }
+        } else if (scfn == SegmentCombinationFn.PRODUCT) {
+          combinationFn = productCombinationFn;
+          if (rufn == RangeUpdateFn.ADDITION) {
+            ruf = productQuerySumUpdate;
+            lruf = lproductQuerySumUpdate;
+          } else if (rufn == RangeUpdateFn.ASSIGN) {
+            ruf = productQueryAssignUpdate;
+            lruf = lproductQueryAssignUpdate;
+          } else if (rufn == RangeUpdateFn.MULTIPLICATION) {
+            ruf = productQueryMulUpdate;
+            lruf = lproductQueryMulUpdate;
+          }
+        } else {
+          throw new UnsupportedOperationException(
+              "Combination function not supported: " + scfn);
+        }
+  }
+
   public GenericSegmentTree(
       long[] values,
       SegmentCombinationFn segmentCombinationFunction,
@@ -187,71 +255,7 @@ public class GenericSegmentTree {
     t = new Long[N];
     lazy = new Long[N];

-    // Select the specified combination function
-    if (segmentCombinationFunction == SegmentCombinationFn.SUM) {
-      combinationFn = sumCombinationFn;
-      if (rangeUpdateFunction == RangeUpdateFn.ADDITION) {
-        ruf = sumQuerySumUpdate;
-        lruf = lsumQuerySumUpdate;
-      } else if (rangeUpdateFunction == RangeUpdateFn.ASSIGN) {
-        ruf = sumQueryAssignUpdate;
-        lruf = lsumQueryAssignUpdate;
-      } else if (rangeUpdateFunction == RangeUpdateFn.MULTIPLICATION) {
-        ruf = sumQueryMulUpdate;
-        lruf = lsumQueryMulUpdate;
-      }
-    } else if (segmentCombinationFunction == SegmentCombinationFn.MIN) {
-      combinationFn = minCombinationFn;
-      if (rangeUpdateFunction == RangeUpdateFn.ADDITION) {
-        ruf = minQuerySumUpdate;
-        lruf = lminQuerySumUpdate;
-      } else if (rangeUpdateFunction == RangeUpdateFn.ASSIGN) {
-        ruf = minQueryAssignUpdate;
-        lruf = lminQueryAssignUpdate;
-      } else if (rangeUpdateFunction == RangeUpdateFn.MULTIPLICATION) {
-        ruf = minQueryMulUpdate;
-        lruf = lminQueryMulUpdate;
-      }
-    } else if (segmentCombinationFunction == SegmentCombinationFn.MAX) {
-      combinationFn = maxCombinationFn;
-      if (rangeUpdateFunction == RangeUpdateFn.ADDITION) {
-        ruf = maxQuerySumUpdate;
-        lruf = lmaxQuerySumUpdate;
-      } else if (rangeUpdateFunction == RangeUpdateFn.ASSIGN) {
-        ruf = maxQueryAssignUpdate;
-        lruf = lmaxQueryAssignUpdate;
-      } else if (rangeUpdateFunction == RangeUpdateFn.MULTIPLICATION) {
-        ruf = maxQueryMulUpdate;
-        lruf = lmaxQueryMulUpdate;
-      }
-    } else if (segmentCombinationFunction == SegmentCombinationFn.GCD) {
-      combinationFn = gcdCombinationFn;
-      if (rangeUpdateFunction == RangeUpdateFn.ADDITION) {
-        ruf = gcdQuerySumUpdate;
-        lruf = lgcdQuerySumUpdate;
-      } else if (rangeUpdateFunction == RangeUpdateFn.ASSIGN) {
-        ruf = gcdQueryAssignUpdate;
-        lruf = lgcdQueryAssignUpdate;
-      } else if (rangeUpdateFunction == RangeUpdateFn.MULTIPLICATION) {
-        ruf = gcdQueryMulUpdate;
-        lruf = lgcdQueryMulUpdate;
-      }
-    } else if (segmentCombinationFunction == SegmentCombinationFn.PRODUCT) {
-      combinationFn = productCombinationFn;
-      if (rangeUpdateFunction == RangeUpdateFn.ADDITION) {
-        ruf = productQuerySumUpdate;
-        lruf = lproductQuerySumUpdate;
-      } else if (rangeUpdateFunction == RangeUpdateFn.ASSIGN) {
-        ruf = productQueryAssignUpdate;
-        lruf = lproductQueryAssignUpdate;
-      } else if (rangeUpdateFunction == RangeUpdateFn.MULTIPLICATION) {
-        ruf = productQueryMulUpdate;
-        lruf = lproductQueryMulUpdate;
-      }
-    } else {
-      throw new UnsupportedOperationException(
-          "Combination function not supported: " + segmentCombinationFunction);
-    }
+    SetCombinationFnAndRuf(segmentCombinationFunction, rangeUpdateFunction);

     buildSegmentTree(0, 0, n - 1, values);
   }
```

## Self-assessment: Way of working

Our assessment is that we are currently on the In Use state. We have our key practices and tools, such as communication channels, meeting routines, programming language and Github repos, selected and well established. For different assignments and along the way, we also discuss and consider which practices and tools are suitable, such as management tools and issue and branch usage on Github.

We have improved our communication and our routines since the team foundation, leading to easier and better re-assessment of our practices. At the team foundation, we agreed on some key practices, but it frequently happened that interpretations differed or work was slowed down because not everything was clearly established. Now, we inspect and adapt our practices frequently, which leads to more efficient work. However these assessments are not systematic, and it would be beneficial to have a standardized way of performing and recording them.

We have a feedback system for our work, where we comment and give feedback on pull requests on Github. However, we are still lacking a feedback or inspection system for our way-of-working, and while team members can raise questions during our meetings we have no standard procedure for work-related check-ins.

To reach the next state, our team would have take more time to collectively and systematically re-assess the tools in use as well way-of-working.

## Overall experience

What are your main take-aways from this project? What did you learn?

One major take-away from this lab was branch coverage and code coverage tests. This was new to most of the group members and we all have gotten a better understanding of the process. Another take-away is the notion of code-complexity and how it can be improved by refactoring.

Is there something special you want to mention here?

Yes! It was very interesting to implement our own "tool" for code coverage to get a good picture of how code coverage test are done in automatic code coverage tools.
