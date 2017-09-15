---
layout: post
title: 线段树区段更新
category: [线段树]
tags: [数据结构]
---

*线段树(Segment tree)， 是一种树形结构， 它的每一个区间存储一个区间或线段， 用于快速动态查询连续区间信息。 线段树的实质是一种二叉树。*  

## 线段树的操作
### build
基础知识， 略。
### query
基础知识， 略。
### update
单点更新， 略。

## 区段更新
线段树的区段更新用到了一个lazy标记（也有说是延迟标记）的概念。 在执行update操作时， 并不向下执行到叶子节点， 而是在完全覆盖的区间做一个lazy标记， 不再向下更新。 在执行query操作时， 如果发现查询到的区间有lazy标记， 先将lazy标记向下push， 然后再查询。
{% highlight cpp %}
void push_down(int idx){
	if(lazy[idx]){
		lazy[2*idx] = lazy[idx];
		lazy[2*idx + 1] = lazy[idx];

		deal_with(tree[2*idx]);
		deal_with(tree[2*idx + 1]);

		lazy[idx] = 0;
	}
}
{% endhighlight %}
{% highlight cpp %}
void push_up(int idx){
	deal_with(tree[idx]);
}
{% endhighlight %}

## POJ 3468
Proble Description : [Portal](http://poj.org/problem?id=3468){:target="_blank"}  
Code :   
{% highlight cpp %}
#include<iostream>
#include<cstdio>
#include<cstring>
using namespace std;

#define ll long long

const int MAXN = 1e5 + 10;

ll tree[4*MAXN];
ll lazy[4*MAXN];

void build(int idx, int l, int r){
    if(l >= r){
        cin >> tree[idx];
        return ;
    }
    int mid = (l + r)/2;
    build(2*idx, l, mid);
    build(2*idx + 1, mid + 1, r);
    tree[idx] = tree[2*idx] + tree[2*idx + 1];
}

void push_up(int idx){
    tree[idx] = tree[2*idx] + tree[2*idx + 1];
}

void push_down(int idx, int l, int r){
    if(lazy[idx] != 0){
        lazy[2*idx] += lazy[idx];
        lazy[2*idx + 1] += lazy[idx];
        int mid = (l + r)/2;
        tree[2*idx] += lazy[idx]*(mid - l + 1);
        tree[2*idx + 1] += lazy[idx]*(r - mid);
        lazy[idx] = 0;
    }
}

ll query(int idx, int l, int r, int ql, int qr){
    if(ql <= l && qr >= r){
        return tree[idx];
    }

    push_down(idx, l, r);

    int mid = (l + r)/2;
    if(qr <= mid){
        return query(2*idx, l, mid, ql, qr);
    } else if(ql > mid){
        return query(2*idx + 1, mid + 1, r, ql, qr);
    } else{
        return query(2*idx, l, mid, ql, mid) + query(2*idx + 1, mid + 1, r, mid + 1, qr);
    }
}

void update(int idx, int l, int r, int ul, int ur, int v){
    if(ul <= l && ur >= r){
        lazy[idx] += v;
        tree[idx] += (r - l + 1)*v;
        return ;
    }

    if(l == r)  return ;

    push_down(idx, l, r);

    int mid = (l + r)/2;
    if(ur <= mid){
        update(2*idx, l, mid, ul, ur, v);
    } else if(ul > mid){
        update(2*idx + 1, mid + 1, r, ul, ur, v);
    } else{
        update(2*idx, l, mid, ul, mid, v);
        update(2*idx + 1, mid + 1, r, mid + 1, ur, v);
    }

    push_up(idx);
}

int main(){
    ios::sync_with_stdio(false);
    int n, m;
    cin >> n >> m;
        build(1, 1, n);
        for(int i = 0; i < m; ++i){
            char ch;
            int a, b, c;
            cin >> ch >> a >> b;
            if(ch == 'Q'){
                cout << query(1, 1, n, a, b) << endl;
            } else if(ch == 'C'){
                cin >> c;
                update(1, 1, n, a, b, c);
            }
        }

    return 0;
}
{% endhighlight %}

### POJ 2528
Problem Description : [Portal](http://poj.org/problem?id=2528){:target="_blank"}  
Code :   
{% highlight cpp %}
#include<iostream>
#include<cstdio>
#include<cstring>
using namespace std;

#define ll long long

const int MAXN = 1e5 + 10;

ll tree[4*MAXN];
ll lazy[4*MAXN];

void build(int idx, int l, int r){
    if(l >= r){
        cin >> tree[idx];
        return ;
    }
    int mid = (l + r)/2;
    build(2*idx, l, mid);
    build(2*idx + 1, mid + 1, r);
    tree[idx] = tree[2*idx] + tree[2*idx + 1];
}

void push_up(int idx){
    tree[idx] = tree[2*idx] + tree[2*idx + 1];
}

void push_down(int idx, int l, int r){
    if(lazy[idx] != 0){
        lazy[2*idx] += lazy[idx];
        lazy[2*idx + 1] += lazy[idx];
        int mid = (l + r)/2;
        tree[2*idx] += lazy[idx]*(mid - l + 1);
        tree[2*idx + 1] += lazy[idx]*(r - mid);
        lazy[idx] = 0;
    }
}

ll query(int idx, int l, int r, int ql, int qr){
    if(ql <= l && qr >= r){
        return tree[idx];
    }

    push_down(idx, l, r);

    int mid = (l + r)/2;
    if(qr <= mid){
        return query(2*idx, l, mid, ql, qr);
    } else if(ql > mid){
        return query(2*idx + 1, mid + 1, r, ql, qr);
    } else{
        return query(2*idx, l, mid, ql, mid) + query(2*idx + 1, mid + 1, r, mid + 1, qr);
    }
}

void update(int idx, int l, int r, int ul, int ur, int v){
    if(ul <= l && ur >= r){
        lazy[idx] += v;
        tree[idx] += (r - l + 1)*v;
        return ;
    }

    if(l == r)  return ;

    push_down(idx, l, r);

    int mid = (l + r)/2;
    if(ur <= mid){
        update(2*idx, l, mid, ul, ur, v);
    } else if(ul > mid){
        update(2*idx + 1, mid + 1, r, ul, ur, v);
    } else{
        update(2*idx, l, mid, ul, mid, v);
        update(2*idx + 1, mid + 1, r, mid + 1, ur, v);
    }

    push_up(idx);
}

int main(){
    ios::sync_with_stdio(false);
    int n, m;
    cin >> n >> m;
        build(1, 1, n);
        for(int i = 0; i < m; ++i){
            char ch;
            int a, b, c;
            cin >> ch >> a >> b;
            if(ch == 'Q'){
                cout << query(1, 1, n, a, b) << endl;
            } else if(ch == 'C'){
                cin >> c;
                update(1, 1, n, a, b, c);
            }
        }

    return 0;
}
{% endhighlight %}