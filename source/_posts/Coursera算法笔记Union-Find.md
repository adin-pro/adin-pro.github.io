---
title: Coursera算法笔记Union-Find
mathjax: true
date: 2020-04-25 17:46:33
tags:
- 算法
- Coursera
categories: 数据结构与算法
---

Coursera上的算法课程笔记,对应书本为Algorithm 4th Edition。这一章介绍的是Union-Find问题。

<!-- more -->

## Dynamic connectivity

Given a set of N objects 
- union command: connect  two objects
- Find/connected query: is there  a path connecting the two objects?


We assume "Is connected to" is an equivalence relation:
- reflexive: p is connected to p (自己永远连接到自己)
- symmetric: 连接是对称的
- transitive: 连接是可以传递的
  
**Connected Component**: maximum set of objects that are mutually(相互) 
onnected, like {0} {1,4,5} {7,9} {8}所以连接在一起的节点都是一个集合里面的元素

- union command: replace two sets of nodes to be connected with a union
- find query: are two nodes in same set

$$
union(1,2) \; union(3,4) \; union(5,6) \;union(1,5) \; union (2,6)\\
two\; sets:\{1,2,5,6\}\;\{3,4\} \\
connected(1,6):true
$$


| public class UF  | Func |
| ----------- | ----------- |
| UF(int N) | initilize union-find data structure |
|void union(int p, int q)|add connection between q and p|
| boolean connected(int p, int q) | are two nodes in the same component? |

## Quick Find [eager approach]

<img src='image1.png' width = '800' title = 'eager approach'>

- Find: check if id[p] = id[q]
- Union: change all entries whose values equals to id[p] and id[q]

>the method "Find" is quick, o(1) ,so named quick-find
but "union" is too expensive , o(n), if you want to do n times union operation on a array of n,  o(n^2)


## Quick Union [lazy approach]

> we try to avoid work unitl we have to

<img src='image2.png' width = '800' title = 'lazy approach'>

- find: check if p and q have the same root O(n)
- union: to merge components containing p and q, set thr id of p's root to the id of q's root, O(n)

> It still works not very well

## Quick Union Improvements

### Weighted quick union
避免将大树🌳放在小树  下面，让数目尽可能平衡

decease the depth of the tree, making depth of any node is at most

### Flatten! Path Compression

<img src='image3.png' width = '800' title = 'flatten'>

let the node point to the roof every time the  method 'roof' called

[Assignment1: Percolation](https://coursera.cs.princeton.edu/algs4/assignments/percolation/specification.php)

## Assignment

```py

One Complementation
/* *****************************************************************************
 *  Name:Adin
 *  Date:2020.1.1
 *  Description:Percolation demo
 **************************************************************************** */

import edu.princeton.cs.algs4.WeightedQuickUnionUF;

public class Percolation {

    // num of columns and rows
    private int num;
    // WQUUF object
    private WeightedQuickUnionUF uf;
    // status sites blockes=0 open=1
    private boolean[][] sites;


    // creates n-by-n grid, with all sites initially blocked
    public Percolation(int n) {
        num = n;
        check(n);
        uf = new WeightedQuickUnionUF(n * n + 1);
        sites = new boolean[n][n];
        for (int i = 0; i < num; i++) {
            uf.union(value(1, i + 1), num * num);
        }
    }

    // check invalidation
    private void check(int num1) {
        if (num1 <= 0 || num1 > this.num)
            throw new IllegalArgumentException("Invalid range of n");
    }

    // calculate the # of sites
    private int value(int row, int col) {
        return (row - 1) * num + col - 1;
    }

    // opens the site (row, col) if it is not open already
    public void open(int row, int col) {
        check(row);
        check(col);
        if (isOpen(row, col)) return;
        // open the site
        sites[row - 1][col - 1] = true;
        if (row > 1 && isOpen(row - 1, col)) uf.union(value(row, col), value(row - 1, col));
        if (row < num && isOpen(row + 1, col)) uf.union(value(row, col), value(row + 1, col));
        if (col > 1 && isOpen(row, col - 1)) uf.union(value(row, col), value(row, col - 1));
        if (col < num && isOpen(row, col + 1)) uf.union(value(row, col), value(row, col + 1));
    }

    // is the site (row, col) open?
    public boolean isOpen(int row, int col) {
        check(row);
        check(col);
        return sites[row - 1][col - 1];
    }

    // is the site (row, col) full?
    public boolean isFull(int row, int col) {
        return isOpen(row, col) && (uf.find(value(row, col)) == uf.find(num * num));

    }

    // returns the number of open sites
    public int numberOfOpenSites() {
        int count = 0;
        for (int i = 0; i < num; i++)
            for (int j = 0; j < num; j++)
                if (sites[i][j]) count++;
        return count;
    }

    // does the system percolate?
    public boolean percolates() {
        if (num == 1) {
            return isOpen(1, 1);
        }

        for (int i = 0; i < num; i++)
            if (uf.find(value(num, i + 1)) == uf.find(num * num))
                return true;
        return false;
    }

    public static void main(String[] args) {

    }
}
```


```py

/* *****************************************************************************
 *  Name:Adin
 *  Date:2020.1.1
 *  Description:
 **************************************************************************** */

import edu.princeton.cs.algs4.StdOut;
import edu.princeton.cs.algs4.StdRandom;
import edu.princeton.cs.algs4.StdStats;

public class PercolationStats {

    private double mean;
    private double stddev;
    private double confilo;
    private double confihi;
    private int[] openSites;
    private int openNum;

    // perform independent trials on an n-by-n grid
    public PercolationStats(int n, int trials) {
        if (n <= 0 || trials <= 0)
            throw new IllegalArgumentException("Invalid input : n or trials musu > 0 !");

        double[] threshold = new double[trials];
        for (int i = 0; i < trials; i++) {
            // start of one trial
            // initialize the sites
            Percolation perco = new Percolation(n);
            // StdRandom.setSeed(StdRandom.getSeed());
            // choose a random number of open sites
            openSites = StdRandom.permutation(n * n);
            // start this trial
            for (int j = 0; j < n * n; j++) {
                perco.open(openSites[j] / n + 1, openSites[j] % n + 1);
                // if percolate, stop the trial
                if (perco.percolates()) {
                    threshold[i] = (j + 1) * 1.0 / (n * n) * 1.0;
                    break;
                }
                threshold[i] = 1.0;
            }

            // end of one trial
        }
        // end of all trials
        mean = StdStats.mean(threshold);
        stddev = StdStats.stddev(threshold);
        confilo = mean - stddev * 1.96 / Math.sqrt((double) trials);
        confihi = mean + stddev * 1.96 / Math.sqrt((double) trials);

    }

    // sample mean of percolation threshold
    public double mean() {
        return mean;
    }

    // sample standard deviation of percolation threshold
    public double stddev() {
        return stddev;
    }

    // low endpoint of 95% confidence interval
    public double confidenceLo() {
        return confilo;
    }

    // high endpoint of 95% confidence interval
    public double confidenceHi() {
        return confihi;
    }

    // test client (see below)
    public static void main(String[] args) {
        StdOut.print("Trials start!\n");
        PercolationStats stats = new PercolationStats(Integer.parseInt(args[0]),
                                                      Integer.parseInt(args[1]));
        StdOut.print("Work completed\n mean = " + stats.mean() + "\n stddev = " +
                             stats.stddev() + "\n 95% confidence interval = [" +
                             stats.confidenceLo() + " , " + stats.confidenceHi() + "] \n");
    }

}

```


<img src='happy.png' width = '800' title = '真正的快乐哈哈哈哈'>