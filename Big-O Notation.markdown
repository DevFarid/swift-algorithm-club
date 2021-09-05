# A note on Big-O notation

Imagine you're putting away clean dishes. There are a few ways to do it, but most people probably aren't going to put away each thing, one at a time. Instead, if possible, most people are going to make stacks of similar items, and put multiple things away at once. This different "algorithm", or way of doing it, might not change much if you only have 20 dishes, but if you have 1,000, it could save a lot of time.

Similarly, when we're in the middle of trying to get something working, we can write code that's equivalent to putting one dish in the cupboard at a time. When we test the app, it might work fine with 10 or 100 items, but later, a user might add thousands of items and start to see lags, slowdowns, or even crashes. If we plan for that though, we can make sure our projects can handle whatever is thrown their way.

That leads us developers to need to have a way to get an idea how intensive different algorithms are. For apps, we're mainly concerned with how much space things take, and how long they run before accopmlishing their goal. We don't want to build something that takes 10 minutes to search, or that loads more data than the device can store.

This is where Big-O notation comes in. It gives us several categories to understand how long and how large various approaches are, relative to the number of items its working with (**n**). For example, if we're trying to find something in an array, we might get lucky and find it at position 0. The worst-case scenario, though, is that we'll need to loop through the entire array to find it at the very end. Since the worst-case scenario involves checking every element, we consider this an O(n) operation (pronounced Oh-of-n). This means that the time it takes to complete the search scales up one to one with the number of items in the array. If it has 20 items, the search could take up to 20 units of time. If it has 100 items, the search could take 100 units of time.

With how fast devices are these days, the difference between searching an array of 20 items vs 100 items might just be a fraction of a second. With more data, though, this could easily scale up. Some social apps can have millions of users, so searching for someone this way might take 100,000,000 times longer than a more efficient search algorithm. That kind of wait time could convince users to drop your service. Using a different search algorithm that runs in O(log n) time, for example, would cut the running time from 100,000,000 units of time to just 8 - a huge improvement that can be the edge your app needs to attract users.

Figuring out the Big-O of an algorithm is usually done through mathemtical analysis. We're skipping the math here, but it's useful to know what the different values mean, so here's a handy table.  

Big-O | Name | Description
------| ---- | -----------
**O(1)** | constant | **This is the best.** The algorithm always takes the same amount of time, regardless of how much data there is. Example: looking up an element of an array by its index.
**O(log n)** | logarithmic | **Pretty great.** These kinds of algorithms halve the amount of data with each iteration. If you have 100 items, it takes about 7 steps to find the answer. With 1,000 items, it takes 10 steps. And 1,000,000 items only take 20 steps. This is super fast even for large amounts of data. Example: binary search.
**O(n)** | linear | **Good performance.** If you have 100 items, this does 100 units of work. Doubling the number of items makes the algorithm take exactly twice as long (200 units of work). Example: sequential search.
**O(n log n)** | "linearithmic" | **Decent performance.** This is slightly worse than linear but not too bad. Example: the fastest general-purpose sorting algorithms.
**O(n^2)** | quadratic | **Kinda slow.** If you have 100 items, this does 100^2 = 10,000 units of work. Doubling the number of items makes it four times slower (because 2 squared equals 4). Example: algorithms using nested loops, such as insertion sort.
**O(n^3)** | cubic | **Poor performance.** If you have 100 items, this does 100^3 = 1,000,000 units of work. Doubling the input size makes it eight times slower. Example: matrix multiplication.
**O(2^n)** | exponential | **Very poor performance.** You want to avoid these kinds of algorithms, but sometimes you have no choice. Adding just one bit to the input doubles the running time. Example: traveling salesperson problem.
**O(n!)** | factorial | **Intolerably slow.** It literally takes a million years to do anything.  



![Comparison of Big O computations](https://upload.wikimedia.org/wikipedia/commons/7/7e/Comparison_computational_complexity.svg)



Below are some examples for each category of performance:

**O(1)**

  The most common example with O(1) complexity is accessing an array index. Since we know the position of the item we are looking for, it doesn't really matter if the array has 20 items or 1,000,000 items.

  ```swift
  let value = array[5]
  ```

  Another example of O(1) is pushing and popping from Stack.


**O(log n)**

  ```swift
  var j = 1
  while j < n {
    // do constant time stuff
    j *= 2
  }
  ```  

  Instead of simply performing the operation once for every item (n), 'j' is increased by 2 times itself in each run, which represents logarithmic complexity. A real-world example of this is Binary Search.


**O(n)**

  ```swift
  for element in array {
    print(element)
  }
  ```

  Array Traversal and Linear Search are examples of O(n) complexity, since they both do some work with every element in the collection.  


**O(n log n)**

  ```swift
  for i in 0..<n {
  var j = 1
    while j < n {
      j *= 2
      // do constant time stuff
    }
  }
  ```

  OR

  ```swift
  for i in 0..<n {
    func index(after i: Int) -> Int? { // multiplies `i` by 2 until `i` >= `n`
      return i < n ? i * 2 : nil
    }
    for j in sequence(first: 1, next: index(after:)) {
      // do constant time stuff
    }
  }
  ```

  Merge Sort and Heap Sort are examples of O(n log n) complexity, since they go through every element in the array, and for each of them, they perform work that falls under logarithmic complexity.


**O(n^2)**

  ```swift
  for i  in 0..<n {
    for j in 1..<n {
      // do constant time stuff
    }
  }
  ```

  Traversing a simple 2-D array and Bubble Sort are examples of O(n^2) complexity. For each of the n elements, these need to do n amount of work.


**O(n^3)**

  ```swift
  for i in stride(from: 0, to: n, by: 1) {
    for j in stride(from: 1, to: n, by: 1) {
      for k in stride(from: 1, to: n, by: 1) {
        // do constant time stuff
      }
    }
  }
  ```  

**O(2^n)**

  Algorithms with running time O(2^N) are often recursive algorithms that solve a problem of size N by recursively solving two smaller problems of size N-1. That is, for every one of n items, the algorithm is doing n-1 amount of work twice. 
  The following example prints all the moves necessary to solve the famous "Towers of Hanoi" problem for N disks.

  ```swift
  func solveHanoi(n: Int, from: String, to: String, spare: String) {
    guard n >= 1 else { return }
    if n > 1 {
        solveHanoi(n: n - 1, from: from, to: spare, spare: to)
        solveHanoi(n: n - 1, from: spare, to: to, spare: from)
    }
  }
  ```
  
  Another example is trying to find the value of a certain element in the Fibonacci Sequence.
  


**O(n!)**

  This level of complexity is so incredibly slow that it's actually rare to run into it in a real example. For an n of 5, it takes 120 units of time. For an n of 10, it takes 3,628,800 units of time. 
  
  The most trivial example of a function that takes O(n!) is given below.

  ```swift
  func nFactFunc(n: Int) {
    for i in 0..<n {
      nFactFunc(n: n - 1)
    }
  }
  ```

Often you don't need math to figure out what the Big-O of an algorithm is. Instead, you can simply use some logic and intuition. If your code uses a single loop that looks at all **n** elements of your input, the algorithm is **O(n)**. If the code has two nested loops, it is **O(n^2)**. Three nested loops gives **O(n^3)**, and so on.

Note that Big-O notation is an estimate and is only really useful for large values of **n**. For example, the worst-case running time for the [insertion sort](Insertion%20Sort/) algorithm is **O(n^2)**. In theory that is worse than the running time for [merge sort](Merge%20Sort/), which is **O(n log n)**. But for small amounts of data, insertion sort is actually faster, especially if the array is partially sorted already!

If you find this confusing, don't let this Big-O stuff bother you too much. It's mostly useful when comparing two algorithms to figure out which one is better. But in the end you still want to test in practice which one really is the best for the intended use case. And if the amount of data will always be relatively small, then even a slow algorithm will be fast enough for practical use.
