---
layout: post
title: FsUnit Data Driven Tests
---

#{{ page.title }}#

<p class="meta">20 June 2014</p>

First things first, it was with no small joy that I discovered that that FsUnit plays nicely with one of my favorite testing tools, [nCrunch](http://www.ncrunch.net).

Happily on my way developing F# with tests continuously running, it was not long before I was wanting to do data driven tests, and the syntax proved a small challenge.
[Unit Testing Sequences of Events using TestCaseSource, FsUnit, and Xamarin](http://andy-p.github.io/FsTestCaseSource.html) was a helpful resource. Based on that, the following example shows a relatively simple way to perform data driven tests, passing parameters of various types, including a list:

    static member PlaceValuesTestData =
        [|
            [|10,9,[9]|];
            [|10,10,[1;0]|];
            [|10,11,[1;1]|];
            [|10,12,[1;2]|];
            [|10,123,[1;2;3]|];
            [|3,4,[1;1]|]
        |]

    [<TestCaseSource("PlaceValuesTestData")>]
    member x.PlaceValueTests (testData:(int*int*(int list))) = 
        let baseValue, number, expected = testData
        let result = placeValues baseValue number []
        result |> should equal expected

A couple of points worth noting:

1. The TestCaseSource (PlaceValuesTestData) is an array of arrays. Each inner array element is passed to the test function arguments.

2. Each of the inner arrays of PlaceValuesTestData is actually an array of tuples. This is not necessarily immediately obvious, because of the implied (missing) parenthesis.

  The following from F# Interactive should make this clearer:

    > (1,2);;
    val it : int * int = (1, 2)
    > 1,2;;
    val it : int * int = (1, 2)  

So in the example above,

    [|
        [|10,9,[9]|];
        ...
    |]
is the same as

    [|
        [|(10,9,[9])|];
        ...
    |]

That tuple, the first and only element of the array of parameters, is what is passed to the test function, which has one parameter, which is decomposed by pattern on the first line of the test function.
