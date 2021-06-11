## Finding and Adding to a tree

### Objectives 

- Understand the procedure for finding and adding nodes to a tree
- Understand how code finding and adding nodes to a tree 

### Arrays and Linked Lists: Costs and Benefits

Thus far we have talked about two data structures: arrays and linked lists.  Let's quickly review this.

**Arrays**

Remember that arrays work by storing each element a fixed distance from an initial element.  

```text

|'b' |'c' |'d'| ____|___ |___|
 500  508   516   524  532 540
```

Arrays storing elements a fixed distance apart makes them very efficient at retrieving information.  This is because it is very easy to calculate where an element at a specific index lives.  The cost of retrieving an element by its index is big O(1).  Or to put it another way, the cost of retrieving an element at a specific index does not depend on the length of the array.

More costly with arrays is shifting or unshifting an element from an array.  To add an element to the beginning means, that each subsequent element must move down one slot, which therefore take big o (n) time.

```text

|'a' |'b' |'c'| 'd' |___ |___|
 500  508  516  524  532 540
```

**Linked Lists** 

Linked lists are more costly for retrieving an element by an index. This is because each node in a linked list can live at *any address*, and one must visit each node to discover where the next node lives.  Because of this, retrieval costs big o (n).  

However, adding or removing elements to the beginning of the linked list can be done by only changing the node before and after the inserted node.  So shifting and unshifting elements is not dependent on the number of elements in the list.  For example, in the diagram below we insert a node holding the string 'c' by changing what the previous node points to and changing what the inserted node points to.

```text

'a'  ->    'b' ->    'd'
500        1024      35
  1024       35


// Inserting the node with value 'c' in a linked list
'a' ->   'b' ->   'c' ->  'd'
500      1024      42      35
  1024      42       35
```
> To insert the node, we only had to change the second node to point to the inserted node, and have the inserted node point to the proper subsequent node.

Let's see the strengths and weaknesses of linked lists and arrays in a chart:

Data Structure | Adding Elements | Retrieving Elements
| ----------  |:---------:|    -----:|
Arrays |             BigO(n)|      BigO(1)
Linked List |       BigO(1) |      BigO(n)             

So arrays are strong at retrieval but not great at manipulation, while linked lists are stronger at manipulation while weaker at retrieval.  Are trees the tool that can perform with both?  

### Finding or Adding in Trees

Now let's think of the following problem.  Let's write a function called find.  The function will operate such that given a tree's root node, and a new piece of data, it will place that data in the correct place on the tree.  

Ok, how would do you approach solving our problem?  Let's recall our problem solving approach:

1. Give ourselves an example
2. See if our brain can solve the problem
3. Think about how our brain is able to solve the problem, slow down and translate this into words

Ok, when we choose an example, should we use our javascript data structure or the diagram?  

```text
Break down the problem with...

Diagram       or           Javascript

   6     
  /  
 1             vs    {data: '4', rightChild: null, leftChild: null}
  \
   4
```

At this point, let's use the digram to solve the problem in the conceptual zone, and later we can translate to code.  Ok, so now we need to try inserting a node, see how our brain solves the problem and then translate this process into words.

**1. Use an example**


So our tree looks like the following.  **Let's take the number three and insert it into the tree.**   How do we do this, what are the steps?

```text

 3
	   6     
	  /  
	 1
	  \
	   4
```

So we start at the root node, and see that the number three is less than the number six, so we go to the left.  If there is no number to the left, then we would insert the node, however there is.  So now we repeat the process by asking our question again, is our number three greater or less than the value of this next node, one?  It is greater than one, so we go to the right.  We would insert the 3, except that four is there, and again we compare three to the current node, and because three is less than the current node we try to go to the left.  There is no node on the left branch of four, so we insert our node, and update our tree.

```text
   6     
  /  
 1
  \
   4
   /
  3
```

At this point, we should check that we applied this example correctly.  We do so by checking our updated data structure against our definition of a Binary Search Tree.  

* **Binary Search Tree** Each node with data smaller than a parent is to the left, and each node with data larger than its parent is to the right.   

**2. Translate into code**

The first part of translating into code is to summarize our process in words.  Here's our summary: 

1. Take the number we are looking to insert and, starting at the root node, compare it against the current node.  
2. We look to the left or right, depending on whether the new node is smaller or larger respectively.  
3. If there is no existing node, we insert the node there.  Otherwise, we go through the process of comparing again.  

So now, let's try to translate this into code.

```javascript
function findOrAdd(rootNode, newNode){
  let currentNode = rootNode.data
  if(newNode.data < rootNode.data){
      currentNode = rootNode.left
      if(currentNode){
        // do something
      } else {
        currentNode.left = newNode
      }
  } else if(newNode.data > rootNode.data) {
    currentNode = rootNode.right
    if(currentNode){
      // do something
    } else {
      currentNode.right = newNode
    }
  }
}
```
Does this work so far?  Try it with a couple numbers and make sure.  

So now the only question is, what if there is a currentNode, what do we have to do then?  Well, we have to go through the process again.  In other words, our code should really look like the following:

```javascript

function findOrAdd(rootNode, newNode){
  let currentNode = rootNode
  if(newNode.data < rootNode.data){
      currentNode = rootNode.left
      if(currentNode){
        findOrAdd(currentNode, newNode)
      } else {
        rootNode.left = newNode
      }
  } else if(newNode.data > rootNode.data) {
    currentNode = rootNode.right
    if(currentNode){
      findOrAdd(currentNode, newNode)
    } else {
      rootNode.right = newNode
    }
  }
}

```

So yes, our solution is recursive.  The recursive case is to compare the `newNode` against the `currentNode` and move to the right or left accordingly.  The base case is when after moving to the right or left, we do not find a node.  At that point, we assign the newNode to its proper position and stop.  

Let's see that we followed our steps for coding that we outlined in our previous section:

**3. Check that the code works**

it looks like we still need to do number three, and see that our final code works.  

```javascript
  let rootNode = {data: 6, left: {data: 1, left: null, right: {data: 4, left: null, right: null} }, right: null }
  let newNode = {data: 3, leftChild: null, rightChild: null}
  findOrAdd(rootNode, newNode)
  newNode == rootNode.left.right.left
  // true
```

Ok, everything works.  

### Summary

Here we went through the process of inserting a new node into a tree.  Our mechanism to do so was to stay with our diagram of a tree, choose an example and solve the problem.  Then we checked that we properly inserted a new element in our diagram by going back to our definition of a binary search tree.  Finally, we translated this solution into code by summarizing how we solve the problem in words, translating these words to code, and checking our solution with data.

In the lab that follows, we will ask you to write code that will add a node to the tree if it does not exist, but return true if node with the data already exists.  In other words, it will either find or add the node.

Let's explore more in the lab that follows.

<p class='util--hide'>View <a href='https://learn.co/lessons/find-or-add-trees'>Find Or Add Trees</a> on Learn.co and start learning to code for free.</p>
