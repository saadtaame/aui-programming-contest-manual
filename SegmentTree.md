# Segment Tree Data Structure
## Description:
Given an array. We perform some changes and queries on continuous segments. It mainly supports 2 operations:
  1. Update one element in the array .
  2. Find/Query a result of an operation (add, subtract, multiply, gcd, lcm...) on elements on some segment (range). 


## Input: 
Array A, N: Size of the Array, K: Number of operations, 
Type of the operation if it is UPDATE : it will require 2 arguments which are the index that should be updated and the new value. If it is QUERY: it will require 2 arguments which are left index and the right index of the segment/range. 

## Output: 
Answer of the performed operation (add, subtract, multiply, gcd, lcm...) on a given segment. 

## Implementation:


```c++
#include <cstdio>
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

struct SegmentTree {
    vector<int> t;
    int n;
    
    SegmentTree() {}
    
    SegmentTree(int n_) {
        t.resize(4 * n_);
        fill(t.begin(), t.end(), 0);
        
        n = n_;
    }
    
    void build(vector<int> &a, int p, int l, int r) {
        if(l == r)
            t[p] = a[l];
        else {
            int mid = (l + r) / 2;
            build(a, 2 * p, l, mid);
            build(a, 2 * p + 1, mid + 1, r);
            t[p] = t[2 * p] + t[2 * p + 1];
        }
    }
    
    /* [l,r] is node interval and [i,j] is query interval
       p is current node in tree
    */
    int query(int p, int l, int r, int i, int j) {
        if(r < i || j < l)
            return 0; /* Neutral element (does not change output) */
            
        if(l >= i && r <= j)
            return t[p];
            
        int mid = (l + r) / 2;
        int leftAns = query(2 * p, l, mid, i, j);
        int rightAns = query(2 * p + 1, mid + 1, r, i, j);
        return (leftAns + rightAns);
    }
    
    void update(int p, int l, int r, int pos, int newValue) {
        if(l == r)
            t[p] = newValue;
        else {
            int mid = (l + r) / 2;
            if(pos <= mid)
                update(2 * p, l, mid, pos, newValue);
            else
                update(2 * p + 1, mid + 1, r, pos, newValue);
                
            t[p] = t[2 * p] + t[2 * p + 1];
        }
    }
};

SegmentTree build(vector<int> &a) {
    
    int n = (int)a.size();
    int l = 0;
    int r = n - 1;
    int p = 1;
    
    SegmentTree tree(n);
    tree.build(a, p, l, r);
    
    return tree;
}

int query(SegmentTree &tree, int l, int r) {
    return tree.query(1, 0, tree.n - 1, l, r);
}

void update(SegmentTree &tree, int pos, int value) {
    tree.update(1, 0, tree.n - 1, pos, value);
}

vector<int> V;

int main(void){
    int N, K, l, r, p;
    cin >> N >> K;
    char op;

    SegmentTree tree(N);
    
    while(K--){
        cin >> op;
        if(op == 'F'){
            cin >> p;
            if(query(tree, p - 1, p - 1) == 0)
                update(tree, p - 1, 1);
            else 
                update(tree, p - 1, 0);
        } 
        else {
            cin >> l >> r;
            cout << query(tree, l - 1, r - 1) << endl;
        }
    }

    return 0;
}
```
