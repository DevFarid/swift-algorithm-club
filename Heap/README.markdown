# Heap

> This topic has been tutorialized [here](https://www.raywenderlich.com/160631/swift-algorithm-club-heap-and-priority-queue-data-structure)

A heap is a [binary tree](../Binary%20Tree/) inside an array, so it does not use parent/child pointers. A heap is sorted based on the "heap property" that determines the order of the nodes in the tree.

Common uses for heap:

- To build [priority queues](../Priority%20Queue/).
- To support [heap sorts](../Heap%20Sort/).
- To compute the minimum (or maximum) element of a collection quickly.
- To impress your non-programmer friends.

## The heap property

There are two kinds of heaps: a *max-heap* and a *min-heap* which are different by the order in which they store the tree nodes.

In a max-heap, parent nodes have a greater value than each of their children. In a min-heap, every parent node has a smaller value than its child nodes. This is called the "heap property", and it is true for every single node in the tree.

An example:

![A max-heap](Images/Heap1.png)

This is a max-heap because every parent node is greater than its children. `(10)` is greater than `(7)` and `(2)`. `(7)` is greater than `(5)` and `(1)`.

As a result of this heap property, a max-heap always stores its largest item at the root of the tree. For a min-heap, the root is always the smallest item in the tree. The heap property is useful because heaps are often used as a [priority queue](../Priority%20Queue/) to access the "most important" element quickly.

> **Note:** The root of the heap has the maximum or minimum element, but the sort order of other elements are not predictable. For example, the maximum element is always at index 0 in a max-heap, but the minimum element isn’t necessarily the last one. -- the only guarantee you have is that it is one of the leaf nodes, but not which one.

## How does a heap compare to regular trees?

A heap is not a replacement for a binary search tree, and there are similarities and differences between them. Here are some main differences:


**Order of the nodes.** In a [binary search tree (BST)](../Binary%20Search%20Tree/), the left child must be smaller than its parent, and the right child must be greater. This is not true for a heap. In a max-heap both children must be smaller than the parent, while in a min-heap they both must be greater.

**Memory.** Traditional trees take up more memory than just the data they store. You need to allocate additional storage for the node objects and pointers to the left/right child nodes. A heap only uses a plain array for storage and uses no pointers.

**Balancing.** A binary search tree must be "balanced" so that most operations have **O(log n)** performance. You can either insert and delete your data in a random order or use something like an [AVL tree](../AVL%20Tree/) or [red-black tree](../Red-Black%20Tree/), but with heaps we don't actually need the entire tree to be sorted. We just want the heap property to be fulfilled, so balancing isn't an issue. Because of the way the heap is structured, heaps can guarantee **O(log n)** performance.

**Searching.** Whereas searching is fast in a binary tree, it is slow in a heap. Searching isn't a top priority in a heap since the purpose of a heap is to put the largest (or smallest) node at the front and to allow relatively fast inserts and deletes.

## The tree inside an array

An array may seem like an odd way to implement a tree-like structure, but it is efficient in both time and space.

This is how we are going to store the tree from the above example:

	[ 10, 7, 2, 5, 1 ]

That's all there is to it! We don't need any more storage than just this simple array.

So how do we know which nodes are the parents and which are the children if we are not allowed to use any pointers? Good question! There is a well-defined relationship between the array index of a tree node and the array indices of its parent and children.

If `i` is the index of a node, then the following formulas give the array indices of its parent and child nodes:

    parent(i) = floor((i - 1)/2)
    left(i)   = 2i + 1
    right(i)  = 2i + 2

Note that `right(i)` is simply `left(i) + 1`. The left and right nodes are always stored right next to each other.

Let's use these formulas on the example. Fill in the array index and we should get the positions of the parent and child nodes in the array:

| Node | Array index (`i`) | Parent index | Left child | Right child |
|------|-------------|--------------|------------|-------------|
| 10 | 0 | -1 | 1 | 2 |
| 7 | 1 | 0 | 3 | 4 |
| 2 | 2 | 0 | 5 | 6 |
| 5 | 3 | 1 | 7 | 8 |
| 1 | 4 | 1 | 9 | 10 |

Verify for yourself that these array indices indeed correspond to the picture of the tree.

> **Note:** The root node `(10)` does not have a parent because `-1` is not a valid array index. Likewise, nodes `(2)`, `(5)`, and `(1)` do not have children because those indices are greater than the array size, so we always have to make sure the indices we calculate are actually valid before we use them.

Recall that in a max-heap, the parent's value is always greater than (or equal to) the values of its children. This means the following must be true for all array indices `i`:

```swift
array[parent(i)] >= array[i]
```

Verify that this heap property holds for the array from the example heap.

As you can see, these equations allow us to find the parent or child index for any node without the need for pointers. It is more complicated than just dereferencing a pointer, but that is the tradeoff: we save memory space but pay with extra computations. Fortunately, the computations are fast and only take **O(1)** time.

It is important to understand this relationship between array index and position in the tree. Here is a larger heap which has 15 nodes divided over four levels:

![Large heap](Images/LargeHeap.png)

The numbers in this picture are not the values of the nodes but the array indices that store the nodes! Here is the array indices correspond to the different levels of the tree:

![The heap array](Images/Array.png)

For the formulas to work, parent nodes must appear before child nodes in the array. You can see that in the above picture.

Note that this scheme has limitations. You can do the following with a regular binary tree but not with a heap:

![Impossible with a heap](Images/RegularTree.png)

You can not start a new level unless the current lowest level is completely full, so heaps always have this kind of shape:

![The shape of a heap](Images/HeapShape.png)

> **Note:** You *could* emulate a regular binary tree with a heap, but it would be a waste of space, and you would need to mark array indices as being empty.

Pop quiz! Let's say we have the array:

	[ 10, 14, 25, 33, 81, 82, 99 ]

Is this a valid heap? The answer is yes! A sorted array from low-to-high is a valid min-heap. We can draw this heap as follows:

![A sorted array is a valid heap](Images/SortedArray.png)

The heap property holds for each node because a parent is always smaller than its children. (Verify for yourself that an array sorted from high-to-low is always a valid max-heap.)

> **Note:** But not every min-heap is necessarily a sorted array! It only works one way. To turn a heap back into a sorted array, you need to use [heap sort](../Heap%20Sort/).

## More math!

In case you are curious, here are a few more formulas that describe certain properties of a heap. You do not need to know these by heart, but they come in handy sometimes. Feel free to skip this section!

The *height* of a tree is defined as the number of steps it takes to go from the root node to the lowest leaf node, or more formally: the height is the maximum number of edges between the nodes. A heap of height *h* has *h + 1* levels.

This heap has height 3, so it has 4 levels:

![Large heap](Images/LargeHeap.png)

A heap with *n* nodes has height *h = floor(log2(n))*. This is because we always fill up the lowest level completely before we add a new level. The example has 15 nodes, so the height is `floor(log2(15)) = floor(3.91) = 3`.

If the lowest level is completely full, then that level contains *2^h* nodes. The rest of the tree above it contains *2^h - 1* nodes. Fill in the numbers from the example: the lowest level has 8 nodes, which indeed is `2^3 = 8`. The first three levels contain a total of 7 nodes, i.e. `2^3 - 1 = 8 - 1 = 7`.

The total number of nodes *n* in the entire heap is therefore *2^(h+1) - 1*. In the example, `2^4 - 1 = 16 - 1 = 15`.

There are at most *ceil(n/2^(h+1))* nodes of height *h* in an *n*-element heap.

The leaf nodes are always located at array indices *floor(n/2)* to *n-1*. We will make use of this fact to quickly build up the heap from an array. Verify this for the example if you don't believe it. ;-)

Just a few math facts to brighten your day.

## What can you do with a heap?

There are two primitive operations necessary to make sure the heap is a valid max-heap or min-heap after you insert or remove an element:

- `shiftUp()`: If the element is greater (max-heap) or smaller (min-heap) than its parent, it needs to be swapped with the parent. This makes it move up the tree.

- `shiftDown()`. If the element is smaller (max-heap) or greater (min-heap) than its children, it needs to move down the tree. This operation is also called "heapify".

Shifting up or down is a recursive procedure that takes **O(log n)** time.

Here are other operations that are built on primitive operations:

- `insert(value)`: Adds the new element to the end of the heap and then uses `shiftUp()` to fix the heap.

- `remove()`: Removes and returns the maximum value (max-heap) or the minimum value (min-heap). To fill up the hole left by removing the element, the very last element is moved to the root position and then `shiftDown()` fixes up the heap. (This is sometimes called "extract min" or "extract max".)

- `removeAtIndex(index)`: Just like `remove()` with the exception that it allows you to remove any item from the heap, not just the root. This calls both `shiftDown()`, in case the new element is out-of-order with its children, and `shiftUp()`, in case the element is out-of-order with its parents.

- `replace(index, value)`: Assigns a smaller (min-heap) or larger (max-heap) value to a node. Because this invalidates the heap property, it uses `shiftUp()` to patch things up. (Also called "decrease key" and "increase key".)

All of the above take time **O(log n)** because shifting up or down is expensive. There are also a few operations that take more time:

- `search(value)`. Heaps are not built for efficient searches, but the `replace()` and `removeAtIndex()` operations require the array index of the node, so you need to find that index. Time: **O(n)**.

- `buildHeap(array)`: Converts an (unsorted) array into a heap by repeatedly calling `insert()`. If you are smart about this, it can be done in **O(n)** time.

- [Heap sort](../Heap%20Sort/). Since the heap is an array, we can use its unique properties to sort the array from low to high. Time: **O(n lg n).**

The heap also has a `peek()` function that returns the maximum (max-heap) or minimum (min-heap) element, without removing it from the heap. Time: **O(1)**.

> **Note:** By far the most common things you will do with a heap are inserting new values with `insert()` and removing the maximum or minimum value with `remove()`. Both take **O(log n)** time. The other operations exist to support more advanced usage, such as building a priority queue where the "importance" of items can change after they have been added to the queue.

## Inserting into the heap

Let's go through an example of insertion to see in details how this works. We will insert the value `16` into this heap:

![The heap before insertion](Images/Heap1.png)

The array for this heap is `[ 10, 7, 2, 5, 1 ]`.

The first step when inserting a new item is to append it to the end of the array. The array becomes:

	[ 10, 7, 2, 5, 1, 16 ]

This corresponds to the following tree:

![The heap before insertion](Images/Insert1.png)

The `(16)` was added to the first available space on the last row.

Unfortunately, the heap property is no longer satisfied because `(2)` is above `(16)`, and we want higher numbers above lower numbers. (This is a max-heap.)

To restore the heap property, we swap `(16)` and `(2)`.

![The heap before insertion](Images/Insert2.png)

We are not done yet because `(10)` is also smaller than `(16)`. We keep swapping our inserted value with its parent, until the parent is larger or we reach the top of the tree. This is called **shift-up** or **sifting** and is done after every insertion. It makes a number that is too large or too small "float up" the tree.

Finally, we get:

![The heap before insertion](Images/Insert3.png)

And now every parent is greater than its children again.

The time required for shifting up is proportional to the height of the tree, so it takes **O(log n)** time. (The time it takes to append the node to the end of the array is only **O(1)**, so that does not slow it down.)

## Removing the root

Let's remove `(10)` from this tree:

![The heap before removal](Images/Heap1.png)

What happens to the empty spot at the top?

![The root is gone](Images/Remove1.png)

When inserting, we put the new value at the end of the array. Here, we do the opposite: we take the last object we have, stick it up on top of the tree, and restore the heap property.

![The last node goes to the root](Images/Remove2.png)

Let's look at how to **shift-down** `(1)`. To maintain the heap property for this max-heap, we want to the highest number of top. We have two candidates for swapping places with: `(7)` and `(2)`. We choose the highest number between these three nodes to be on top. That is `(7)`, so swapping `(1)` and `(7)` gives us the following tree.

![The last node goes to the root](Images/Remove3.png)

Keep shifting down until the node does not have any children or it is larger than both its children. For our heap, we only need one more swap to restore the heap property:

![The last node goes to the root](Images/Remove4.png)

The time required for shifting all the way down is proportional to the height of the tree which takes **O(log n)** time.

> **Note:** `shiftUp()` and `shiftDown()` can only fix one out-of-place element at a time. If there are multiple elements in the wrong place, you need to call these functions once for each of those elements.

## Removing any node

The vast majority of the time you will be removing the object at the root of the heap because that is what heaps are designed for.

However, it can be useful to remove an arbitrary element. This is a general version of `remove()` and may involve either `shiftDown()` or `shiftUp()`.

Let's take the example tree again and remove `(7)`:

![The heap before removal](Images/Heap1.png)

As a reminder, the array is:

	[ 10, 7, 2, 5, 1 ]

As you know, removing an element could potentially invalidate the max-heap or min-heap property. To fix this, we swap the node that we are removing with the last element:

	[ 10, 1, 2, 5, 7 ]

The last element is the one that we will return; we will call `removeLast()` to remove it from the heap. The `(1)` is now out-of-order because it is smaller than its child, `(5)` but sits higher in the tree. We call `shiftDown()` to repair this.

However, shifting down is not the only situation we need to handle. It may also happen that the new element must be shifted up. Consider what happens if you remove `(5)` from the following heap:

![We need to shift up](Images/Remove5.png)

Now `(5)` gets swapped with `(8)`. Because `(8)` is larger than its parent, we need to call `shiftUp()`.

## Creating a heap from an array

It can be convenient to convert an array into a heap. This just shuffles the array elements around until the heap property is satisfied.

In code it would look like this:

```swift
  private mutating func buildHeap(fromArray array: [T]) {
    for value in array {
      insert(value)
    }
  }
```

We simply call `insert()` for each of the values in the array. Simple enough but not very efficient. This takes **O(n log n)** time in total because there are **n** elements and each insertion takes **log n** time.

If you didn't gloss over the math section, you'd have seen that for any heap the elements at array indices *n/2* to *n-1* are the leaves of the tree. We can simply skip those leaves. We only have to process the other nodes, since they are parents with one or more children and therefore may be in the wrong order.

In code:

```swift
  private mutating func buildHeap(fromArray array: [T]) {
    elements = array
    for i in stride(from: (nodes.count/2-1), through: 0, by: -1) {
      shiftDown(index: i, heapSize: elements.count)
    }
  }
```

Here, `elements` is the heap's own array. We walk backwards through this array, starting at the first non-leaf node, and call `shiftDown()`. This simple loop puts these nodes, as well as the leaves that we skipped, in the correct order. This is known as Floyd's algorithm and only takes **O(n)** time. Win!

## Searching the heap

Heaps are not made for fast searches, but if you want to remove an arbitrary element using `removeAtIndex()` or change the value of an element with `replace()`, then you need to obtain the index of that element. Searching is one way to do this, but it is slow.

In a [binary search tree](../Binary%20Search%20Tree/), depending on the order of the nodes, a fast search can be guaranteed. Since a heap orders its nodes differently, a binary search will not work, and you need to check every node in the tree.

Let's take our example heap again:

![The heap](Images/Heap1.png)

If we want to search for the index of node `(1)`, we could just step through the array `[ 10, 7, 2, 5, 1 ]` with a linear search.

Even though the heap property was not conceived with searching in mind, we can still take advantage of it. We know that in a max-heap a parent node is always larger than its children, so we can ignore those children (and their children, and so on...) if the parent is already smaller than the value we are looking for.

Let's say we want to see if the heap contains the value `8` (it doesn't). We start at the root `(10)`. This is not what we are looking for, so we recursively look at its left and right child. The left child is `(7)`. That is also not what we want, but since this is a max-heap, we know there is no point in looking at the children of `(7)`. They will always be smaller than `7` and are therefore never equal to `8`; likewise, for the right child, `(2)`.

Despite this small optimization, searching is still an **O(n)** operation.

> **Note:** There is a way to turn lookups into a **O(1)** operation by keeping an additional dictionary that maps node values to indices. This may be worth doing if you often need to call `replace()` to change the "priority" of objects in a [priority queue](../Priority%20Queue/) that's built on a heap.

## The code

See [Heap.swift](Heap.swift) for the implementation of these concepts in Swift. Most of the code is quite straightforward. The only tricky bits are in `shiftUp()` and `shiftDown()`.

You have seen that there are two types of heaps: a max-heap and a min-heap. The only difference between them is in how they order their nodes: largest value first or smallest value first.

Rather than create two different versions, `MaxHeap` and `MinHeap`, there is just one `Heap` object and it takes an `isOrderedBefore` closure. This closure contains the logic that determines the order of two values. You have probably seen this before because it is also how Swift's `sort()` works.

To make a max-heap of integers, you write:

```swift
var maxHeap = Heap<Int>(sort: >)
```

And to create a min-heap you write:

```swift
var minHeap = Heap<Int>(sort: <)
```

I just wanted to point this out, because where most heap implementations use the `<` and `>` operators to compare values, this one uses the `isOrderedBefore()` closure.

## See also

[Heap on Wikipedia](https://en.wikipedia.org/wiki/Heap_%28data_structure%29)

*Written for the Swift Algorithm Club by [Kevin Randrup](http://www.github.com/kevinrandrup) and Matthijs Hollemans*
