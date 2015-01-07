---
layout: post
title: "如何写伪代码 pseudocode"
date: 2014-06-11 08:29:31 +0800
comments: true
categories: 
---

##为什么写伪代码

##如何写伪代码

伪代码有很多种写法，以下方法参考常见伪代码写法，比如算法导论

>Based on many requests, we changed the syntax (as it were) of our pseudocode. We now use “D” to indicate assignment and “==” to test for equality, just as C, C++, Java, and Python do. Likewise, we have eliminated the keywords do and then and adopted “//” as our comment-to-end-of-line symbol. We also now use dot-notation to indicate object attributes. Our pseudocode remains procedural, rather than object-oriented. In other words, rather than running methods on objects, we simply call procedures, passing objects as parameters.





##We use the following conventions in our pseudocode.
Indentation indicates block structure. For example, the body of the for loop that begins on line 1 consists of lines 2–8, and the body of the while loop that begins on line 5 contains lines 6–7 but not line 8. Our indentation style applies to if-else statements2 as well. Using indentation instead of conventional indicators of block structure, such as begin and end statements, greatly reduces clutter while preserving, or even enhancing, clarity.3
The looping constructs while, for, and repeat-until and the if-else conditional construct have interpretations similar to those in C, C++, Java, Python, and Pascal.4 In this book, the loop counter retains its value after exiting the loop, unlike some situations that arise in C++, Java, and Pascal. Thus, immediately after a for loop, the loop counter’s value is the value that first exceeded the for loop bound. We used this property in our correctness argument for insertion sort. The for loop header in line 1 is for j D 2 to A:length, and so when this loop terminates, j D A:length C 1 (or, equivalently, j D n C 1, since n D A:length). We use the keyword to when a for loop increments its loop counter in each iteration, and we use the keyword downto when a for loop decrements its loop counter. When the loop counter changes by an amount greater than 1, the amount of change follows the optional keyword by.
The symbol “//” indicates that the remainder of the line is a comment.A multiple assignment of the form i D j D e assigns to both variables i and j the value of expression e; it should be treated as equivalent to the assignment j D e followed by the assignment i D j .
Variables (such as i, j, and key) are local to the given procedure. We shall not use global variables without explicit indication.
We access array elements by specifying the array name followed by the in- dex in square brackets. For example, AŒi􏰇 indicates the ith element of the array A. The notation “: :” is used to indicate a range of values within an ar- ray. Thus, AŒ1 : : j 􏰇 indicates the subarray of A consisting of the j elements AŒ1􏰇;AŒ2􏰇;:::;AŒj􏰇.
We typically organize compound data into objects, which are composed of attributes. We access a particular attribute using the syntax found in many object-oriented programming languages: the object name, followed by a dot, followed by the attribute name. For example, we treat an array as an object with the attribute length indicating how many elements it contains. To specify the number of elements in an array A, we write A:length.
We treat a variable representing an array or object as a pointer to the data rep- resenting the array or object. For all attributes f of an object x, setting y D x causes y:f to equal x:f. Moreover, if we now set x:f D 3, then afterward not only does x:f equal 3, but y:f equals 3 as well. In other words, x and y point to the same object after the assignment y D x.Our attribute notation can “cascade.” For example, suppose that the attribute f is itself a pointer to some type of object that has an attribute g. Then the notation x:f:g is implicitly parenthesized as .x:f/:g. In other words, if we had assigned y D x:f, then x:f:g is the same as y:g.Sometimes, a pointer will refer to no object at all. In this case, we give it the special value NIL.
We pass parameters to a procedure by value: the called procedure receives its own copy of the parameters, and if it assigns a value to a parameter, the change is not seen by the calling procedure. When objects are passed, the pointer to the data representing the object is copied, but the object’s attributes are not. For example, if x is a parameter of a called procedure, the assignment x D y within the called procedure is not visible to the calling procedure. The assignment x:f D 3, however, is visible. Similarly, arrays are passed by pointer, so that a pointer to the array is passed, rather than the entire array, and changes to individual array elements are visible to the calling procedure.
A return statement immediately transfers control back to the point of call in the calling procedure. Most return statements also take a value to pass back to the caller. Our pseudocode differs from many programming languages in that we allow multiple values to be returned in a single return statement.
The boolean operators “and” and “or” are short circuiting. That is, when we evaluate the expression “x and y” we first evaluate x. If x evaluates to FALSE, then the entire expression cannot evaluate to TRUE, and so we do not evaluate y. If, on the other hand, x evaluates to TRUE, we must evaluate y to determine the value of the entire expression. Similarly, in the expression “x or y” we eval- uate the expression y only if x evaluates to FALSE. Short-circuiting operators allow us to write boolean expressions such as “x ¤ NIL and x:f D y” without worrying about what happens when we try to evaluate x:f when x is NIL.
The keyword error indicates that an error occurred because conditions were wrong for the procedure to have been called. The calling procedure is respon- sible for handling the error, and so we do not specify what action to take.







1. 赋值

```
i = 2
j = 1

```

2. 条件


3. 循环

```
for j = 2 to n
	some code

while i > 0 and j > 0
	some code here
		

```

