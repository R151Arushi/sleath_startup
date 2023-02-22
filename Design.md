# DESIGN DOCUMENT: Algorithmic Trading using custom growth parameter and random sampling

## Overview

 We have created a custom paramater to analyze price movements for a given portfolio. 
 We generate n-k+1 datapoints for n portfolio values. We bootstrap this dataset to infer a most likely price movement
 of the security/portfolio along with the probability of attaining this price movement at the next timestep.
 We use this method to compare several portfolios, enter the optimal one, exit within a short time period, and repeat.

## The Parameter

Given n datapoints, start at the first datapoint and calculate {average growth} for k successive datapoints. Repeat for 
successive groups of k points. As an example, for k=3, we process our price data by returning a list of the form
((avg growth between prices 1,2,3),(avg growth between prices 2,3,4,) ... (avg growth between prices n-2 ,n-1,n))
## Function Definitions

### generateParameter(prices, k)
Given a list of prices of length n, return a list of n-k-1 parameter values, where k is the interval size.

### growth(data, growth_fn)
Return the growth of the data using growth_fn.

### bootstrap(parameter_values, num_iter, px)
Return the array of dimension [num_iter], where each element is a weighted sum of len(parameter_values) randomly sampled elements 
(with replacement) from data with distribution px. 

px is a list representing the probability distribution on parameter values, where P(x) = probaiblity of x getting selected.


### distribution(paramters)
Generate a probability distribution on the parameters and return it as a list p, where p[i] = probability of element i getting picked and sum(p) = 1.


### cluster(sampled_parameter_values)
What we want: partition our list of PVs by value. Identify and return the center of
the densest partition(s).
Defintion of k-means clustering:

> k-means clustering aims to partition n observations into k clusters in which each observation belongs to the cluster with 
> the nearest mean (cluster centers or cluster centroid), serving as a prototype of the cluster.

Remember all of the discussion of sorting our monte-carlo output into "bands" based on their value? This gives us clusters
instead of fixed bands, meaning we let go of the idea of standardizing bands and give more freedom to our
final PV/Probability graph.

kmeans1D returns two lists: 'clusters' and centroids. 'clusters' contains which cluster the to which 
the corresponding element of the original list belongs. We simply need to find out the most popular cluster,
and its corresponding probability is the proportion in which it exists in the list of labels.

Example:

```python
    import kmeans1d

    x = [4.0, 4.1, 4.2, 4.3, 4.1, -50, 200.2, 200.4, 200.9, 80, 100, 102]
    k = 4

    clusters, centroids = kmeans1d.cluster(x, k)

    print(clusters)   # [1, 1, 1, 1, 1, 0, 3, 3, 3, 2, 2, 2]
    print(centroids)  # [-50.0, 4.1, 94.0, 200.5]
```

Here, Cluster 1 with centroid 4.1 is the densest cluster with probability 5/12. Equivalently we can find "probabilities"
for the other clusters too.


### 
