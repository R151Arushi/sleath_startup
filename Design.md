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

### bootstrap(parameter_values, num_iter, k)
Return the array of dimension [num_iter], where each element is a weighted sum of k randomly sampled elements 
(with replacement) from data.

### cluster(sampled_parameter_values)
What we want: partition our list of PVs by value. Identify and return the center of
the densest partition(s).
Library to look at: ckmeans. 

### 