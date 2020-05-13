---
layout: post
title:  "Stair Climing"
date:   2020-05-12 09:55:39 -0400
categories: jekyll update
---
## Solving the stair climbing problem
The climbing stairs problem is a classic interview question and a good question to get your feet wet with dynamic programming. The climbing stairs problem is as follows: “You are climbing a stair case. It takes n steps to reach to the top. Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?” As a human we could easily calculate the number of steps for 4,5, or even 6 stairs. However, as the numbers get larger we end up having to do more and more of these calculations, of greatly increasing numbers each time. Okay so now we know why we need to do a computer to be able to solve this but how? How will our program figure out how many ways there are to reach a certain step? Let’s find that out.

Before we begin any programming challenge we need to determine what we know and we are trying to find out. We know that a staircase with 0 steps can be climbed in 0 ways, a staircase with a step can be climbed in one way, by taking a single one step. A staircase with two steps can be climbed in two ways, either by taking two one steps or by taking a single two step. This information is important as it will serve as the base cases for our recursive functions. What are we trying to find out? We are trying to find out how many ways we climb n stairs. Keep that in mind while we tackle this challenge. 

If we pick an arbitrary staircase length, say 6 how can we figure out how many ways to reach the top? Let’s work backwards for a moment here, and pretend we are already standing on stair 6. How did we reach this stair? We could have gotten here by either taking a one step from stair 5 or a two step from stair 4. So we know the number of ways to reach the 6th stair is the number of ways to reach the 4th stair + the number of ways to reach the 6th stair. How did we reach these 4th and 5th stairs, by taking one step or by taking two steps. This is true for every stair as we descend our staircase. Look at the tree below to understand how the works.

## An initial recursive solution
Looking at the tree above we can see we descend all the way down the tree until we reach a question we can answer, this is the principle of recursion. In recursion we keep passing down the problem until we reach someone (a call) that can give us an answer. We then take this answer and pass it upwards towards the person who asked the question. The answer is provided by our base cases which I mentioned earlier. Let’s now try to implement this function recursively and see where that takes us. Study the code below to understand why it works (I am writing the code below in C#):

        public int StairCliming(int stairs)
        {

            //if we are on stairs 0, 1, or 2 we can simply return the stair as it
            //corresponds to the number of ways to climb to that stair
            if (stairs <= 2)
            {
                return stairs;
            }

            //if we are on stairs 3 and onwards, we need to know how many ways there
            //are to reach this stair, accomplished by finding out how many ways
            //there are to reach stairs stair - 1 and stair - 2
            return StairCliming(stairs - 1) + StairCliming(stairs - 2);
        }

This Code is all well and good but there's a catch, it runs in O(2^n). For newcomers to big-O notations this means exponential time and is very bad, looking at the tree we see that each call to n generates 2 additional calls, except for the base cases. For small values of stairs this code runs just fine but starts to really slow down as we reach larger numbers. So let's make it better.

## Repeating Work
Look carefully at the tree below and notice the nodes (circles with numbers inside them) that I've circled in red. Notice anything in common? They are repeated multiple times across the tree, sometimes many times. That's the fatal flaw with our recursive solution, we are repeating the same work over and over again. If only there was a way to somehow reuse this information. There is! Using a technique called **_memoization_** (not memorization) we save the results of calls we've already made and reuse them for later instead of calculating the same answer over again. Memoization sounds fancy but it's simply saving information for later, like if I asked you to calculate a + b, and then a + b + c, you could just write down the answer to a + b and reuse it when I ask you to calculate a + b + c instead of recalculating instead.

So how do we memoize, well for our inital memoization example we will us a **Dictionary** to store results we've already saved. In C# dictionaries take a key and a value, using the key to index the dictionary when we need an answer. Think of it as a notepad with two columns one is the name of our answer, and the other is the answer itself. Each time we make a recursive we are first going to look in our dictionary and see if we have the answer we want. If it is, great! We just get that answer and return it, if not we have to calculate the answer ourselves. The key distinction here is that we won't be reapeting calculations, take a look at the tree below and see all the work we are saving.


Below is the code we use to implement our StairClimbing function using memoization.

        Dictionary<int, int> cache = new Dictionary<int, int>();

        public int StairClimbingWithMemoization(int stairs)
        {
            if (stairs <= 2)
            {
                return stairs;
            }

            //we check our cache to see if we have already computed the answer
            if (cache.ContainsKey(stairs))
            {
                return cache[stairs];
            }

            //we compute the answer we need
            int ans =  StairClimbingWithMemoization(stairs - 1) + StairClimbingWithMemoization(stairs - 2);

            // we store the answer so we don't have to compute it again
            cache[stairs] = ans;

            return ans;
        }

Look at how much faster it runs compared to the old recursive solution when choosing a stairs value of 45:

    Climbing stairs without memoization
    1836311903
    Time elapsed: 00:00:06.7514066

    Climbing stairs with memoization
    1836311903
    Time elapsed: 00:00:00.0003445

But why is it so much faster? It is so much faster because it runs in linear time O(n), that's a huge improvement! Since it only calculates each answer once, we can pass in large values of n and still receive a quick answer, in fact we will suffer from integer overflow before we ever suffer from an excessive run time (this could of course be easily fixed using the long data type instead of int, which holds 64 bit signed integers vs int's 32 bit signed integers).

## Improvments
The above solution is pretty good, surely we can make it better though. We can improve it but eliminating recursion all together.
## An interative solution

## Conclusion

