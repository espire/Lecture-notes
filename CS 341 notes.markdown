>Eli Spiro's lecture notes  
>University of Waterloo  
>CS 341: Algorithms  

What CS 341 is about
====================
* Main idea: how to design efficient algorithms for problems we might encounter while programming
* More specifically:
	* Basic paradigms to try
	* certain graph algorithms that are of use
	* proving algorithm correctness
	* assessing algorithm efficiency
	* limits to computation
		* intractable problems (and what that means)
		* uncomputable problems

Why another course in algorithms?
---------------------------------
* CS 146: basic algorithms and storing information
* CS 240: advanced data structures used "as is"
* CS 341: how to solve problems when there is no pre-made solution

Course Overview
---------------
* Introduction
	* notation of problem
	* definition of algorithm
	* time complexity
	* asymptotic notation
* Algorithm design techniques
	* Every problem needs to be approached individually.
	* no magic method that always works
	* Creative process often requires the "Aha!" moment of insight.
	* A few standard techniques to try:
		* Greedy algorithms, divide & conquer, dynamic programming
* Graph algorithms
	* Graphs can be used to represent many real world problems.
	* techniques to translate problems into graphs
	* cover several useful and efficient graph algorithms
* NP-completeless (Non-deterministic Polynomial)
	* problems for which we do not know efficient solutions: the famous "P = NP" problem
	* It is important to recognize these problems.

Algorithms in practice
======================
* Algorithm: a way of solving a problem
* Program: an implementation of an algorithm
* Problems have many algorithms, algorithms have many implementations.
* For a problem:
	* Derive an algorithm, then assess efficiency and correctness.
	* If it's efficient, then implement it.

Problem solving in the workplace
--------------------------------
* Problem in an application area, such as
	* how to format a document in a word processor
	* how to perform motion control in a video game
* The problem might not fit any known algorithm.
* What to do:
	* Try to solve using paradigms.
	* Search the literature.
	* Be able to justify your approach early.

What makes an algorithm good?
-----------------------------
* It is correct
	* It always terminates with the right answer.
		* For some algorithms, we must prove that they are correct.
* It is efficient
	* defined more precisely later
		* Several different algorithms might be correct, but differ in efficiency.
		* Another goal is to derive an algorithm that is proven to be as efficient as possible.

Time comparisons
----------------
* see chart in course notes
* Linear algorithms are fast. Exponential algorithms are slooooooooooooooooooooooooow.
* Sometimes we have to settle for Om(n log n). Most of our algorithms will be linear.

Analysis of algorithms
----------------------
* We design algorithms to solve problems.
	* What are valid inputs?
	* What output should we get for each input?
		* correct if for every input the right output is computed
	* running time
		* The running time of algorithm A on input x is the time that algorithm A requires to derive the correct output given x.
			* denoted by R_A(x).
			* We use order notation to describe running time.
			* Note we did not mention running time of the program for A.

	* How do we measure running time?
		* clock time is not a good measure, since that's different for different systems
		* remove the idea of hardware
		* We focus on the number of elementary operations.
	* Elementary operation
		* An operation whose time can be bounded by a constant. It depends on the implementation and not the inputs.
			* arithmetic
			* comparisons
			* program control flow
		* not elementary:
			* max of an array
			* arithmetic operations on multi-precision numbers
			* string concatenation
			* factorials
		* Beware! Some languages (such as perl) offer constructs that are not elementary operations.

Order Notation
==============
* Quicksort runs in O(n^2) time.
* What does that mean?
* Many inputs for a given size n
	* see graph
	* As we increase the bit size of an input, we can pick the worst case for that bit size.
		* Then we can focus on the bit size, rather than specific inputs.
	* Running time might not be monotonic.
		* We could create a curve to follow the running time, but that's complicated.
		* simplify it:
			1. assume a continuous bounding curve going through the max time for each n
			2. use a monotonic bounding curve on that
			3. use a simple upper bounding curve on that

Size of input
-------------
* Ideally, n is the number of bits used to code the input
* We consider the bit length of the parameters specifying the problem
	* number of data items, number of vertices and edges
* Caution: integers go up in bit size log n

Worst case and complexity
-------------------------
* The worst-case running time of A is a function T(.) mapping n to the longest running time for any input size n.
	* T_A(n) = Max{ R_A(x) | |x|=n }
* The time complexity of a problem is the running time of "the best" possible algorithm that solves the problem.
* The average case running time is the expected running time on "average" data.
	* It can be hard to characterize the average.
	* T^avg\_A(n) = (1 / {x: |x|=n}) * ∑\_{x|x|=n} R\_A(x)
* In this course we focus on worst case, not average case.

Why use order notation?
-----------------------
* Describes the runtime of an algorithm without resorting to details about the environment being used
	* analysis will not change with small reasonable changes on the instruction set, so it's widely applicable
	* more~

Definition
----------
* f(n) is in O(g(n)) if there exist constants c > 0 and n\_0 > 0 such that for all n > n\_0, 0 <= f(n) <= cg(n).
* this properly says: f(n) is in O(g(n)), that is to say: f(n) e O(g(n)). Sloppier is f(n) = O(g(n))
* Basically, at some point, f(n) will creep under a multiple of g(n).
* We are considering the asymptotic behaviour, and we do not care about f(n) for small values of n.

Problem 1: Show that f(n) e O(g(n)) when f(n) < 3n^2 + 9 and g(n) = n^2.  
We could set c = 4. Then f(n) = 3n^2 + 9 < 4n^2 which is true when n > 3.  
So we have f(n) e O(g(n)) with n\_0 = 3 and c = 4.  
In general, to show that f(n) <= c\*g(n), we try to show that f(n) <= constant * most significant term in g(n) <= c*g(n))  

If g(n) = n^2 - n - 5? That's = n^2/3 + (n^2 / 3 - n) + (n^2 / 3 - 5)  
n^2 / 3 - n > 0 when n > 3.  
n^2 / 3 - 5 > 0 when n > 4.  
We choose n_0 to be 4.  
n^2 / 3 < g(n) if n > 3 and n > 4, so we set c = 12... 4n^2 <= c*g(n)  
So for n > n_0 = 4 we get f(n) <= 4n^2 <= c*gn if c = 12.

* Using O(.) notation:
	* When discussing an algorithm, it means the worst-case running time. That's not always the best description.
	* We can set our upper bounds as high as we want and still be correct. An efficient algorithm is still e O(2^n).
	* On algorithms, it means the worst-case running time of the best known algorithm.
	* We don't usually see O(n^2 + 3n), though they are technically correct.
	* O(1): bounded by a constant, but unclear without context
	* What about O(n + m)? A function dependent on two variables.
		* f(n, m) e O(m + n) => There is c > 0, n\_0 > 0, m_0 > 0, such that for all n > n\_0 and all m > m\_0, 0 <= f(n, m) < c(m + n)

Omega Notation
--------------
* Reverses the inequality of O-notation
* f(n) e Om(g(n)) if there exist constants c > 0 and n\_0 > 0 such that for all n > n\_0, f(n) >= cg(n) >= 0.
* A lower bound for worst-case running time of an algorithm
* A lower bound for any algorithm for a given problem

Theta Notation
--------------
* Combines O-notation with Om-notation
* To show this, show that f(n) is both O(g(n)) and Om(g(n))
* Useful for expressing the true running time of an algorithm
* Read section 3.2 of the text.

Exponentials
------------
* In practice, an exponential algorithm might be better than a polynomial with a high degree, but in theory, we prefer algorithms of a lower order.
* Where is the crossover?
n^100 = 2^n  
100logn = n  
100 = n/logn  
consider n = 1024, so logn = 10  
100 ~= 102.4

Theta problem: Show that f(n) e Th(g(n)) if f(n) = n^2 + n^3/2 + nlogn, g(n) = (n c 2) = n(n - 1) / 2  
The dominant term is n^2. n^3/2 < n^2, nlogn <= n^2 for n > 2  
f(n) = n^2 + n^3/2 + nlogn < n^2 + n^2 + n^2 = 3n^2  
cg(n) = c/2 * (n^2 -n) = c/2 * (n^2 / 2 + n^2 / 2 - n) so c/2 * n^2 / 2 <= c*gn if n<=2  
So we get f(n) <= 3n^2 <= c/2 * n^2 / 2 <= c*gn if we pick c = 12, so f(n) e O(g(n))  
For n >= 1 we have f(n) = n^2 + n^3/2 + nlogn >= n^2 / 2 >= n(n - 1)/2 = g(n)  
So f(n) e Om(g(n)) with c = 1 and n_0 = 1.  
Combined, we have f(n) e Th(g(n)).

### Impossible problem
f(n) = n|sin(Pi\*n/2)|, g(n) = sqrt(n)  
f(n) is n for n odd, 0 for n even. These functions are incomparable. We can never find c > 0 such that n|sin(Pi\*n/2)| < c*sqrt(n), no matter how large c is. f(n) /e O(g(n)), /e Om(g(n)).  

Show that O(f(n) + g(n)) = O(max{f(n),g(n)})
--------------------------------------------
h(n) e O(f(n) + O(g(n)))
There exists n\_0, c\_1>0 such that for all n > n\_0, h(n) < c\_1 * (f(n) + g(n))
<= c\_1 * (max{f(n), g(n)} + max{f(n), g(n)})
<= 2c\_1 * (max{f(n), g(n)})
so h(n) e O(max{f(n), g(n)})
----------------------------
h(n) e O(max{f(n), g(n)})
so there exists n\_2, c\_2 > 0 such that for all n > n\_2, h(n) <= c\_2 * max{f(n), g(n)}
<= c\_2 * {f(n) + g(n)}
so h(n) e O(f(n) + g(n))

### Little-o Notation
* "grows more slowly than"
* f(n) e o(g(n)) if for every constant c > 0, there exists a constant n\_0 such that for all n > n\_0, 0 <= f(n) < c*g*(n).
* Think of this as: it's true even if positive c is very small.
* An easier definition: f(n) e o(g(n)) if lim\_n->inf f(n)/g(n) = 0
* Useful for questions like "can we sort it in o(nlogn) time?"
* We can be more precise than O-notation
	* Running time is 3n^2 + o(n^3/2)
	
### Little-w (omicron) notation
* "grows more slowly than"
* f(n) e w(g(n)) if for every constant c > 0, there exists a constant n\_0 such that for all n > n\_0, 0 <= f(n) > c*g(n).
* Think of this as: it's true even if positive c is very large.
* An easier definition: f(n) e o(g(n)) if lim\_n->inf f(n)/g(n) = inf
* how do n^k and a^n (a > 1) compare?
	* lim n->inf n^k / a^n
	* Both approach infinity... this is indeterminate form
	* change the denominator to base e and repeatedly differentiate with l'Hopital's rule.
	* or n^k / a^n = (log\_b m)^k / a^logm = (log^k m)/m^loga
	* so lim m->inf log^k(m)/m^p = 0
	* log\_b m e o(m^p)

### Relationships Between Order Notations
see slide

### Algebra of Order Notations
see slide

### Useful Summation Formulae
see slide

Analyzing Pseudo-code
---------------------
* Count the primitive operations instead of just statements
	* For example, count comparisons while sorting
* Do not let overhead dominate
* Be attentive to the logic
	* Is it part of a longer sequence?
	* Is it within a loop?
	* Generally, how often is it executed?
	
### Loops
* Generally work from innermost loops to outermost loops.
* Loops are O or Th of a function of the input size and the control variables
* Rough bound: iterations * worst case cost
* Pricise bound: sum over iterations of cost of each iteration

#### Example loop analysis
* for i = 1..n do {Operation A}
* if A costs ci
	* Rough bound: O(n) for each iteration, O(n^2) total
	* More precise: iteration i costs ci, so the total is
		* ∑(i=1..n) ci = cn(n+1)/2 = cn^2/2 + cn/2 e Th(n^2)
* If A costs cn/2
	* Rough bound: O(n) for each iteration, O(n^2) total
	* ∑(i=1..n)cn/2^i = cn(∑(i=0..n) <= cn(1/(1 - 1/2) - 1) e O(n)
	Since the first iteration alone is Om(n), the total is Th(n)
* if A costs c\*logi
	* Rough bound is O(logn) for each iteration, O(nlogn) total
	* More precise is a tricky sum
	* We can avoid the analysis by showing that the sum is Om(nlogn)
	* To show the total is really Th(nlogn), sum the last half:

### Divide and Conquer
* Mergesort
	* given list of n elements
	* split the list into two lists length n/2
	* sort using merge sort
	* merge the sorted lists
* Analysis:
	* T(n) is the number of comparisons to sort n items. T(1) = 0, T(2) = 1
	* T(n) = 0 if n = 1
	* if n > 1... T(n) = T(fl[n/2] + T(cl[n/2]) + Th(n))

Lecture 4: September 22
-----------------------

### Bentley's Problem
<pre>
Given n numbers in an array: A[1..n]
calculate max(i<=j<=n)∑(R=i..j)A[k]
Example: 31 -41 59 26 -53 58 97 -93 -23
</pre>

#### Solution 1
<pre>
max := 0
for i := 1 to n do
	for j := 1 to n do
		// now computing sum of ahsdfuashdf array A[i]..A[j]
		sum := 0
		for R = i to j do
			sum := sum + A[k]
			// compare to maximum
		if sum > max then max = sum
</pre>
#### Analysis
<pre>
Sum statement is done in O(1); one elementary operation
so the innermost loop costs j - i + 1
for the middle loop each iteration costs j - i + 2, counting the sum > max comparison, total cost is ∑(j=i..n)(j-i+2)
For the outer lop we get a total cost of ∑(i=1..n)∑(j=i..n)(j-i+2)
Let j - i + 2 = m
∑(i=1..m)∑(m=2..n-i+2) when j=i then m=2, when j=n m=n-i+1
= 1/2 * ∑(i=1..n)[(n-i+2)(n-i+3)-2]
Let n-i+1 = p. Ten when i=1 p=n, when i = n p=1
= 1/2 * ∑(p=1..n)[(p+1)(p+2)-2] = 1/2 * ∑(p=1..n)[p^2 + 3p]
= 1/2 * [ n(n+1)(2n+2)/6 + 3n(n+1)/2 ]
= n/6 [n + 5][n + 1] e Th(n^3)
</pre>
#### Solution 2
<pre>
max := 0
for i := 1 to n do
	sum := 0
	for j := 1 to n do
		sum := sum + A[j]
			// sum is now the sum of subarray A[i]..A[j]
		if sum > max then max = sum
</pre>
#### Analysis
<pre>
∑(i=1..n)∑(j=i..n) 2
= 2*∑(i=1..n)(n-i+1)
Let n-i+1 = p, if i = n then p = 1, if i = 1 then p = n
= 2*∑(p=1..n) p
= n(n+1) e Th(n^2)
</pre>

#### Solution 3: Divide and Conquer
* Split the array into two parts
* Get the best solution for each half, using a recursive call
* Also get the best solution with subarray going over the dividing lines
* Finally, get the max of all three possibilities
<pre>
recursive maxsum(low, hi)
	if low > hi return 0 // zero elements
	if low = hi return max(0, A[low])
	mid := (low + hi)/2
	// find max from the partition down to the left
	leftmax := sum := 0
	for i:= mid down to low
		sum := sum + A[i]
		leftmax := max(leftmax, sum)
	//find max on right side
	rightmax := sum := 0
	for i := mid+1 to hi
		sum := sum + A[i]
		rightmax := max(rightmax, sum)
	return max{leftmax + rightmax, maxsum(low,mid), maxsum(med+1, hi)}
</pre>
#### Analysis
* Loopes are not nested. We can show that the worst of each loop is O(n).
* So it would be the same recursive sort function as used in MergeSort (above).

### Floors and Ceilings
* In practice, they are often neglected.
* To be precise, we should not do that!
* It is nearly always possible to evaluate them, but often messy.
* In this course, we gloss over them, which is not always going to be acceptable.

### Solving Recurrences
* Method 1: recursion trees
* Method 2: master theorem
* Method 3: guess and check
* to be studied in the next unit; for now, solve them "ad-hoc"

### Simple Merge Sort Analysis
* A simple analysis gives us a good guess:
<pre>
Assume n = 2^k. then
T(n) <= cn + 2T(n/2) <= cn + 2{cn/2 + 2T(/4)}
	<= 2cn + 4T(n/4) <= ... (continuing)
	<= icn + 2^i T(n/2^i) (in general)
	<= kcn + n T(1) (when i is finally k)
So T(n) e O(n log n) since k = log n.
</pre>
#### More Thorough Analysis
* Write it properly
* Prove T(n) <= cn log n by induction on n.
* Base cas: n = 1. True since T(n) = 0.
* Inductive step: assume T(k_ <= ck log k for k < n, and prove true for k = n.
<pre>
T(n) <= T(fl[n/2]) + T(cl[n/2]) + an
<= c*fl[n/2]*log fl[n/2] + c*fl[n/2]*log cl[n/2] + an
<= c*fl[n/2]*log(n/2) + c*cl[n/2]*log(n) + an
<= c*fl[n/2]*(log n - 1) + c*cl[n/2]*log(*) + an
= cn*log(n) - c*fl[n/2] + an
<= cn*log(n) - c(n/2 - 1/2) + an = cn*log(n) + (a - c/2)n + c/2
<= cn*log(n) for, say, c = 4a
</pre>
### A general algorithmic paradigm
* Divide: separate the problem into subproblems
* Conquer: solve the subproblems recursively
* Combine: use subproblem results ot derive the result
* Examples: binary search, QuickSort, MergeSort

#### When can we use Divide & Conquer?
* original problem is easily decomposable into subproblems
* combining problems is not costly
* subproblems are about the same size

### Multiplication of Large Integers (decimal digits)
* An actual implementation would use words in binary, but the idea is the same
* Primitive operation: multiplying two digits (or words)
* Addition is less expensive so we neglect it now.
	* Later we'll need to show the addition's cost is not asymptotically dominant
#### A better D&C algorithm
* Split the first number into w*10^(n/2) + x, and the second to y*10^(n/2) + z
* uhhh... then stuff. I wasn't paying attention here.
#### Analysis
* Recurrence is now T(n) = 3T(n/2) + Th(n), T(1) = 1
* We eventually show that this has the solution T(n) e O(n^log3)
	* Actually, this can be done better!
