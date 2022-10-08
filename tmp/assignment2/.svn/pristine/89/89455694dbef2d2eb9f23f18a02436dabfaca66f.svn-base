#include <cassert>
#include <cstdio>
#include <iostream>
#include <sstream>
#include <string>

class AVL {
  struct Node {
    int val;
    int height;
    Node* left{nullptr};
    Node* right{nullptr};

    // a leaf node has height 1 while a nil node has height 0.
    Node(const int val) : val{val}, height{1} {}
    void print() { std::cout << val << ' '; }
  };

 public:
  virtual ~AVL() {
    while (root != nullptr) {
      root = remove(root, root->val);
    }
  }

  void Insert(const int val) { root = insert(root, val); }

  void Delete(const int val) { root = remove(root, val); }

  void Preorder() {
    if (root == nullptr) {
      std::cout << "EMPTY\n";
    } else {
      preorder_internal(root);
      std::cout << '\n';
    }
  }

  void Inorder() {
    if (root == nullptr) {
      std::cout << "EMPTY\n";
    } else {
      inorder_internal(root);
      std::cout << '\n';
    }
  }

  void Postorder() {
    if (root == nullptr) {
      std::cout << "EMPTY\n";
    } else {
      postorder_internal(root);
      std::cout << '\n';
    }
  }

  void Print() {
    printBT(root);
    std::cout << "\n\n";
  }

 private:
  int get_height(Node* x) { return x != nullptr ? x->height : 0; }

  void update_height(Node* x) {
    assert(x != nullptr);
    const int l_height = get_height(x->left);
    const int r_height = get_height(x->right);
    x->height = std::max(l_height, r_height) + 1;
  }

  int get_bf(const Node* x) {
    if (x == nullptr) {
      return 0;
    }
    const int bf = get_height(x->left) - get_height(x->right);
    assert(std::abs(bf) <= 2);
    return bf;
  }

  bool is_balance(const Node* x) {
    if (x == nullptr) {
      return true;
    }
    const int bf = get_bf(x);
    return bf >= -1 && bf <= 1;
  }

  // return the successor of the node x.
  Node* get_successor(const Node* x) {
    Node* y = x->right;
    while (y != nullptr && y->left != nullptr) {
      y = y->left;
    }
    return y;
  }

  // return the predecessor of the node x.
  Node* get_predecessor(const Node* x) {
    Node* y = x->left;
    while (y != nullptr && y->right != nullptr) {
      y = y->right;
    }
    return y;
  }

  // return the root node of the subtree:
  //    if no rebalancing, return the node x.
  //    if has rebalancing, return the new root node of the subtree.
  Node* insert(Node* x, const int val) {
    // reaching beyond a leaf node means we shall insert the new node at here.
    if (x == nullptr) {
      return new Node(val);
    }

    // the value val exists, abort this insertion.
    if (val == x->val) {
      return x;
    }

    // insert the value val to the left or right subtree by comparing values.
    if (val < x->val) {
      x->left = insert(x->left, val);
    } else {
      x->right = insert(x->right, val);
    }

    // update height after insertion.
    update_height(x);

    if (is_balance(x)) {
      return x;
    }

    // apply rebalance on this node to restore the balance property.
    return rebalance(x);
  }

  Node* remove(Node* x, const int val) {
    // reaching a leaf node means there's no such node with value val.
    if (x == nullptr) {
      return nullptr;
    }

    // remove the value val to the left or right subtree by comparing values.
    if (val < x->val) {
      x->left = remove(x->left, val);

    } else if (val > x->val) {
      x->right = remove(x->right, val);

    } else {
      // the value val exists, remove it.
      if (x->left == nullptr || x->right == nullptr) {
        // node x has an empty left subtree or an empty right subtree or both.
        Node* y = x->left == nullptr ? x->right : x->left;
        if (y != nullptr) {
          // if at least one child is not null, replace x with it.
          *x = *y;
          free(y);

        } else {
          // both child are null.
          free(x);
          return nullptr;
        }

      } else {
        // node x has both non-empty left subtree and right subtree.
        // replace node x with its inorder successor.
        // Node* y = get_successor(x);
        // x->val = y->val;
        // remove the inorder successor from the right subtree.
        // x->right = remove(x->right, y->val);

        // or replace node x with its inorder predecessor.
        Node* y = get_predecessor(x);
        x->val = y->val;
        // remove the inorder predecessor from the left subtree.
        x->left = remove(x->left, y->val);
      }
    }

    // update height after deletion.
    update_height(x);

    if (is_balance(x)) {
      return x;
    }

    return rebalance(x);
  }

  // return the root of the subtree after rebalancing.
  Node* rebalance(Node* x) {
    const int x_bf = get_bf(x);
    if (x_bf > 0) {
      // left heavy.
      // we shall apply a right rotation to rebalance, but we have to first
      // examine the anchor node of the rotation, i.e. the left child.
      Node* y = x->left;
      const int y_bf = get_bf(y);

      if (y_bf > 0) {
        // left heavy.
        return right_rotate(x);

      } else {
        // right heavy.
        return left_right_rotate(x);
      }

    } else {
      // right heavy.
      // we shall apply a left rotation to rebalance, but we have to first
      // examine the anchor node of the rotation, i.e. the right child.
      Node* y = x->right;
      const int y_bf = get_bf(y);

      if (y_bf < 0) {
        // right heavy.
        return left_rotate(x);

      } else {
        // left heavy.
        return right_left_rotate(x);
      }
    }
  }

  // return the root of the subtree after rotation.
  Node* left_rotate(Node* x) {
    Node* y = x->right;
    x->right = y->left;
    y->left = x;
    // update height after rotation.
    update_height(x);
    update_height(y);
    return y;
  }

  // return the root of the subtree after rotation.
  Node* right_rotate(Node* x) {
    Node* y = x->left;
    x->left = y->right;
    y->right = x;
    // update height after rotation.
    update_height(x);
    update_height(y);
    return y;
  }

  // return the root of the subtree after rotation.
  Node* left_right_rotate(Node* x) {
    Node* y = x->left;
    x->left = left_rotate(y);
    return right_rotate(x);
  }

  // return the root of the subtree after rotation.
  Node* right_left_rotate(Node* x) {
    Node* y = x->right;
    x->right = right_rotate(y);
    return left_rotate(x);
  }

  void preorder_internal(Node* x) {
    if (x == nullptr) {
      return;
    }
    x->print();
    preorder_internal(x->left);
    preorder_internal(x->right);
  }

  void inorder_internal(Node* x) {
    if (x == nullptr) {
      return;
    }
    inorder_internal(x->left);
    x->print();
    inorder_internal(x->right);
  }

  void postorder_internal(Node* x) {
    if (x == nullptr) {
      return;
    }
    postorder_internal(x->left);
    postorder_internal(x->right);
    x->print();
  }

  // note, the binary tree printing code is copied from network.
  // It's only for debugging.
  // @source https://stackoverflow.com/a/51730733
  void printBT(const std::string& prefix, const Node* node, bool isLeft) {
    if (node != nullptr) {
      std::cout << prefix;

      std::cout << (isLeft ? "├──" : "└──");

      // print the value of the node
      std::cout << node->val << std::endl;

      // enter the next tree level - left and right branch
      printBT(prefix + (isLeft ? "│   " : "    "), node->left, true);
      printBT(prefix + (isLeft ? "│   " : "    "), node->right, false);
    }
  }

  void printBT(const Node* node) { printBT("", node, false); }

  Node* root{nullptr};
};

int main() {
  // create an empty AVL tree.
  AVL avl_tree;

  // read a line of input.
  std::string input;
  std::getline(std::cin, input);

  // parse the input to modification moves and feed them to the AVL tree.
  std::string move;
  std::istringstream iss{input};
  while (iss >> move) {
    int val;
    switch (move.front()) {
      case 'A':
        val = std::stoi(move.substr(1));
        avl_tree.Insert(val);
        break;

      case 'D':
        val = std::stoi(move.substr(1));
        avl_tree.Delete(val);
        break;

      default:
        if (move == "PRE") {
          avl_tree.Preorder();
        } else if (move == "IN") {
          avl_tree.Inorder();
        } else if (move == "POST") {
          avl_tree.Postorder();
        }
    }

    // avl_tree.Print();
  }

  return 0;
}
