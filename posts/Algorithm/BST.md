## Definition

A *binary search tree* is a binary tree, where each node has a restriction that the key in any node is larger than the key in its left-child subtree and smaller than any key in its right-child subtree.

## Search

Obviously, if the search key is samller than the key in node, search the key in the node's left subtree;otherwise, search the key in the node's right subtree.

### Insert

It's similar with the *search* immplemention. At first, search the insert key, if retrun null, then we replace it with the insert key.

### Delete

To delete a node *x* by replacing it with its successor.
1.If node *x* is null, delete
2.If node *x* only has one child, then x = x.left/x.right
3.If node *x* has two child; t =x, x = min(t.right), and x.left = t.left, x.right = deleteMin(t.right)

{%highlight C++%}
deleteMin(Node x)
  if x.left == null
    return x.right;
  x.left = deleteMin(x.left)
{%endhighlight%}

## Analysis

The running times of algorithms on binary search trees depend on the shapes of the trees, which, in turn,depend on the order in which keys are inserted. In the best case, a tree with N nodes could be perfectly balanced, with ~ lgN nodes between the root and each null link. In the worst case there could be N nodes on the search path. The balance in typical trees turns out to be much closer to the best case than the worst case.
