---
layout: post
title: Wardrobe Kata
date: "2020-11-11"
---


Having recently done the [wardrobe kata](https://kata-log.rocks/configure-wardrobe-kata), I was asked about the development process that led to [my implementation](https://github.com/hombredequeso/wardrobe-kata), particularly in relation to TDD.

The development process that was followed was roughly:
1. Define the problem in mathematical terms.
2. Think up a solution, in mathematical terms.
3. Implement the solution in Typescript.
4. Determine if the solution dealt with various additional cases for which tests were added requiring a few minor coding changes.

Two points are particularly relevant to TDD. First, typically I would write tests for functions starting with the degenerate and trivial cases. 
For the wardrobe kata this could include:

|distance |wardrobe sizes |expected result  |
|---------|---------------|-----------------|
| 0       | []            | []              |
| 10      | []            | []              |
| 1       | [1]           | [1]             |
| 1       | [2]           | []              |

Often such tests pass with very little effort. While I would normally follow this process, in this case I did not and suffered the consequences later with step 4 when I finally turned to such cases.

Second, in the context of other problems it has been observed that at the point of jumping from degenerate/trivial cases to a single more complex case an entire solution can be forced out that works for all other cases (for instance, see [Mark Seemann's discussion of the diamond kata](https://blog.ploeh.dk/2015/01/10/diamond-kata-with-fscheck/), notably the section on 'Example-Driven Development').

For instance, all the degenerate/trivial cases could be solved as follows:

```TypeScript

export function combinationsEqualling(n: number, amounts: number[]): number[][] {
  if (amounts.length === 1 && amounts[0] === number) {
    return [[amounts[0]]];
  }
  return [];
}

```

It would be possible, albeit annoying, to code for all combinations of amounts with up to two elements, but by the time one gets to three elements you get that unnerving sense that it is necessary to solve for the general case.


For this implementation of the kata, solving this single more complex case, which was essentially the question posed in the kata, was solved in steps 1-3 without any modifications to the single test defined in the kata description.

## 1. Define the problem in mathematical terms
In thinking about the problem, the first step was to express it as a mathematical model. At this point, a concrete problem about wardrobes and available space becomes an abstract problem, expressed in mathematical terms.

* Given a list of numbers, in how many unique ways can they be combined to add up to a particular number without regard for ordering. Essentially, we are looking for the complete collection of ```Set<Bag<Integer>>``` generated from a ```Set<Integer>``` where each ```Bag<Integer>``` adds up to a certain amount.
  * ```Set```: collection of unordered numbers with no duplicates.
  * ```Bag```: collection of unordered numbers allowing duplicates.

There is not necessarily a single abstract way of expressing the problem. this can be a case of oscillating back and forth between the concrete and abstract until an appropriate abstraction is found. This may occur when an abstract expression of the problem yields no solutions, or even as it becomes more clear precisely what is being modelled and the model needs to change. However, in this case the statement of an abstract problem led to a potential solution right from the start.

## 2. Mathematical Solution
Given the abstract expression of the problem, a solution is:
* Create a tree of options. Each node branches out covering all the elements of the ```Set<Integer>```. A node's value is the Bag of values consisting of the node above it, with the new branch value added in.
* Keep going down the tree - when a nodes value equals the desired amount add it to a Set of all the solutions, If the sum of amounts is greater than the total, end that branch at the point.

![Tree of bags](/images/wardrobe-kata-tree.png)

Admittedly, it was probably a particular mathematical solution I had in mind that suggested this approach:
  * Recursively calculate the Cartesian product of the amounts in the list with the values previously calculated.
 
## 3. Implement a solution in Typescript.
Having started from the hint of a Cartesian product as a way to keep track of the currently in progress leaves of a tree together with a list of numbers, the problem then became implementing it in Typescript. At this point the solution was largely in terms of a few algorithms and data structures:
* the end result I was after was a ```Set<Bag<Integer>>```. That meant order of values didn't matter (a bag), and no repeats were allowed (Set). Performing a cartesian product was going to mean taking the following:
  * ```Set<Bag<Integer>>```
  * ```List<Integer>```
  * perform Cartesian product, resulting in ```List<(Integer, Bag<Integer>)>```, which I would reduce to ```List<Bag<Integer>>```.

Given that I was working in typescript, and was aiming not to use any additional libraries, I decide to use Lists, and worry about reducing a ```number[][]``` with repeats to an ```number[][]``` without any repeats and ignoring order as a second step.

Hence, the end result in Typescript is two steps:
1. Keep recursively applying the Cartesian product of array amounts to the current ```Integer[][]```, pulling out arrays in the ```number[][]``` that sum to the desired total, throwing away results over the total, and recursively calling again with the new ```number[][]``` that remain summing to less than the total.
2. The resulting ```number[][]``` has repeated combinations. Treating a ```number[][]``` as a ```Set<Bag<Integer>>``` was implemented as follows:
  * order each of the arrays of numbers.
  * Add each of the arrays to a ```Map<String, number[]>```. Assuming the hash key for each ```number[]``` is unique, this removes all duplicates. In typescript, as it turns out a plain old object can serve as a ```Map<String, *>```. The hash, is the the list .toString(). 

