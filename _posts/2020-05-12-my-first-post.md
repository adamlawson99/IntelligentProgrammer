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

![Recursive tree](_site\assets\Images\ClimbingStairs\tree (1 of 5).jpg)

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

## Improvements
The above solution is pretty good, but surely we can make it better though. We can improve it but eliminating recursion all together. Let's think for a moment about what our recursive solution is doing, it's calling down to get the answer of stairs - 1 and stairs - 2, so at any moment we can look a the solution to the last 2 stairs and get the soltuion to the stair we are currently on. In dynamic programming this is refered to as the bottom up approach, where we look a the smaller subproblems first and use them to compute the answer of the larger problem. I didn't mention it before but the above code using memoization is considered a top down approach, we start from the large problem and break it down into smaller subproblems for which we can find the answer to. Essentially, in the top down approach we navigate the tree from the top to the bottom, each time branching down. In the bottom-up approach we solve the small subproblems first, building up our tree as we go along. For myself, top down is a more intuitive approach and makes more sense to me, but for others the inverse may be try. The only issue with the top down + memoization approach is that we take extra space to store the result of our subproblems, which we can eliminate if we choose the bottom up approach for this problem. The bottom up approach will give an iterative solution in the form of a for loop where we build up each answer and use it to answer our bigger question. For this approach we will us an array to store the answer to our subproblems and use them each time we need to answer the next subproblem. Have a look at the code below:

        public int StairClimbingBottomUp(int stairs)
        {
            if(stairs < 2)
            {
                return stairs;
            }
            //our cache to hold our computed answers
            int[] cache = new int[stairs];

            //cache[0] = stair 1
            cache[0] = 1;
            //cache[1] = stair 2
            cache[1] = 2;

            //calculate the answers for stairs 3..stairs
            for(int i = 2; i < stairs; ++i)
            {
                cache[i] = cache[i - 1] + cache[i - 2];
            }
            //return the answer
            return cache[stairs - 1];
        }

In this implementation we ditch the reliance on external dictionary and instead use an array to store our answers. We then build our solution from the bottom up. By the time we reach stair 6, we've already calculated the answer to stairs 4 and 5, and every stair before it. This method eliminates the need for recursion and is simple and clear to understand. To understand the thought process behind how we would come up with this solution we simply reverse our thinking from the top down approach. Instead of imagining ourselves and the top stair moving down we imagine ourselves on the first stair moving upwards. Imagine standing on the 3rd stair with a notebook that contains the number of ways to reach the 1st and 2nd stair, to figure out how many there are to reach this stair I simply add the ways to reach stair 1 and 2, and the continue to the next stair, writing down the answer each time. By the time I reach the 6th stair I've already caculated stairs 1 through 5 and written the answer down, so I can easily figure out how many ways there are to the stair I'm on.

Can we improve this solution at all? Yes we can! With one final improvement, read on to find out!

## A solution in O(n) time and O(1) space
Attentive readers may have noticed that at each iteration of the for loop we are only using the answer of the last two stairs we visited. Therefore, we don't actually need to store the answer to stairs [0 : stairs - 3], once we calulate them we don't revist them. Instead let's use 3 variables. One that keeps track of the last stair (stair - 1), one that keeps track of the second to last stair (stair - 2), and one for keeping track of our answer. This way we can compute the answer use constant space instead of O(n) space.

        public int StairClimbingConstantSpace(int stairs)
        {
            //the number of ways to climb the stair before the stair we are on, initially this is 1 for stair 1
            int laststair = 1;
            //the number of ways to cling the second to last stair before the stair we are on, initially this is 0, as we start on stair 1
            int secondToLastStair = 0;
            int ans = 0;
            for(int i = 0; i < stairs; ++i)
            {
                //the number of ways to climb the stair we are on
                ans = laststair + secondToLastStair;
                //we move up one stair, so the second to last stair is now the last stair
                secondToLastStair = laststair;
                //the last stair becomes the stair we are on when we move up by one
                laststair = ans;
            }

            return ans;
        }

In the code above you can see we completely removed the need for an array to store our answers, we build bottom up, each time looking at the 2 previous answers to compute the answer we want. This method works because we only ever need the last two stairs to compute the number of ways to reach the stair we are on, and since we start from the bottom stair we guarantee that information will be available to us.

## Conclusion
Congratulations you've made it to the end! I hope you learned something from the post and that dynamic programming became a little more clear. Don't worry if you didn't understand everything at first, it will come with time. The first time I saw this problem I had no clue how to start and how to solve it. Over time you'll begin to develop an intuition for these types of problems and you'll be solving more complex problems in no time. If you have any comments or suggestions please send me an email (I'm working on a adding a comment section) or reach out to me through one of the social media platforms listed below. This is my first blog post so I'm constantly looking to improve and make the experience better for my readers. Stay on the lookout for more content and have a good day!
