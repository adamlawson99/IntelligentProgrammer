---
layout: single
title:  "Parens (draft)"
date:   2020-05-17 09:40:00 -0400
classes: wide
categories: recursion
---
## Generating all pairs of valid parentheses
Recursive functions all very powerful, but can also be non-intuitive and confusing for some. Today, we will be looking at the _parens_ problem found at page 136 in the book **Cracking the Coding Interview**. Tackling this problem will allow us to dive into recursion and also backtracking. By the end of this post I hope you have a better understanding of recursion and of backtracking, both of which are powerful tools for all sorts of problems. (If you're already familiar with backtracking, feel free to skip the next section and go straight to the solution)

### BackTracking
Backtracking is often brought up when it comes to discussion dynamic programming and recursion, let's look at what it is, how to use it, and when to use it.  Backtracking is defined as "a systematic way to iterate through all the possible configurations of a search space."[^1] This definition is nice and all, but what does it mean to 'iterate through all possible configurations of a search space'? A configuration is an option, a decision we make to pursue a certain path in our search space. Our search space is all the options that are available to us after we make a certain decision. Let's imagine we are exploring a maze and we want to find the path that leads to certain point in our maze. Each time we come across an intersection in our maze we have to make decision to continue down one of the paths. Each time we continue down that path to the next intersection we change the configuration we are in and are presented a new set of options. Do I want to go left at this intersection or right? What about the next intersection? We continue making these decisions until we **A.** Reach our goal or **B.** Reach a dead-end. Imagine yourself in the maze and this time let's say we lay down a path of breadcrumbs that allows us to find our back to the previous intersection. If we reach a dead-end what do we do? We follow our breadcrumbs back to the intersection and explore the next unexplored path. We continue this until we've explored all the possible paths from this intersection. Then what? We _backtrack_ to the last intersection which still has unexplored paths and explore those, rinse and repeat until we reach our goal.

![Maze 1](https://github.com/adamlawson99/IntelligentProgrammer/raw/master/assets/Images/Parens/maze1.png)
We reach a dead end so we backtrack to the last intersection that still has unexplored options

![Maze 2](https://github.com/adamlawson99/IntelligentProgrammer/raw/master/assets/Images/Parens/maze2.png)
We reach a dead end so we backtrack to the last intersection that still has unexplored options

![Maze 3](https://github.com/adamlawson99/IntelligentProgrammer/raw/master/assets/Images/Parens/maze3.png)
We reach our goal, so our algorithm ends

It's important to note that a backtracking algorithm doesn't always explore every option. If we had a maze where we reach the goal by turning left right away it's possible that we our algorithm chooses this path first and exits, or it could explore the entirety of the maze and try this path at the very end. So how do we use a backtracking algorithm? 
```cs
        public static bool FindPath(Point current, Point goal)
        {
            //if a path exists return true
            if (current == goal)
            {
                return true;
            }
            //if we reach dead end, this path isn't valid, go back
            if(current == outOfBounds)
            {
                return false;
            }
            //return the value of exploring the left and right subpaths
            return FindPath(left) || FindPath(up);
        }
```
The above is a very simple backtracking where we can either make a move left or up, returning false if we reach a dead-end and true if we reach our goal. When we first start this algorithm, we will continually go left until we reach a dead-end or our goal, as FindPath(left) is the first recursive call we make. Let's imagine we don't find our goal, then what? The last call to Find(left) will return, and then Find(up) will get called. If this hits a dead-end too? Then that function returns as well, and we backtrack to the next Point. This continues until we reach the goal (if it exists, else we return false).

When should we use backtracking? Backtracking should be used anytime we have a problem where we need to **exhaust all**, **visit all**, or **generate all** combinations/solutions to problem. Anytime we are faced with a problem with wording that implies the above, backtracking should jump to the front of our mind.

### The Parentheses Problem
The problem is stated as "Implement an algorithm to print all valid combinations of pairs of parentheses". Notice how the problem says we need to **_print all_** combinations of parentheses, this suggests that a backtracking algorithm may be appropriate for this. So, what is a valid pair of parentheses? A valid pair of parentheses means we have an opening bracket, followed by a closing bracket. For 1 pair of parentheses this means "()", for 2 pairs this means "()()" or "(())". So, the most important step to backtracking is to break down the main problem is to smaller subproblems that we can tackle. In this case we are given the number of pairs of parentheses we need to make pairs for. Therefore, we have 2 * # of pairs parentheses to insert, because each pair has a left and right parenthesis. This means each time we arrive at a step in our algorithm we have to decide to we insert a left or right parenthesis? This is our subproblem. The next step is that we have to impose restrictions on our decisions, we have to make our decision based on some existing knowledge we have about the state of our problem, and the goal we would like to achieve. Clearly we cannot insert a right parenthesis before we have a left parenthesis, but what about after that?

For one, we know we can't insert a right parenthesis before we have inserted a left parenthesis, so every combination must start with a left parenthesis. How about right parentheses? When can we insert them? We can insert right parentheses when the number of left parentheses left to insert in our string is less than the number of right parentheses. Let's say we are given 2 pairs of parentheses to insert, so we start with 2 left parentheses and 2 right parentheses to insert. Our condition above says we have to insert a left parenthesis, because the number of right parentheses is equal to the number of left parentheses. So, now we have 1 left parenthesis and 2 right parentheses left to insert, what can we do? So long as we have at least 1 left parenthesis remaining we can insert it, and we can also insert a right parenthesis, since the amount remaining is greater than the number of left parentheses remaining. We continue this until we reach a point where we have 0 left parentheses and 0 right parentheses remaining, at this point we print the string and recurse upwards, backtracking to the last step that still has options available. Take a look at the tree below to understand this process when given 3 pairs of parentheses.

![ParensTree 1](https://github.com/adamlawson99/IntelligentProgrammer/raw/master/assets/Images/Parens/parenstree-1.jpg)
Our algorithm starts here, with an empty string and 3 left and 3 right parentheses left to insert

![ParensTree 2](https://github.com/adamlawson99/IntelligentProgrammer/raw/master/assets/Images/Parens/parenstree-2.jpg)
If we continue down the left side of our tree we reach a point where we have 0 remaining left parentheses and 3 remaining right parentheses.
We insert right parentheses until both our counts reach 0, we have then successfully generated a string with 3 pairs of valid parentheses.

![ParensTree 3](https://github.com/adamlawson99/IntelligentProgrammer/raw/master/assets/Images/Parens/parenstree-3.jpg)
Here is the fully expanded tree after we've gone through each step. One we hit a leaf node we _backtrack_ upwards until we reach a node that still has options left,
we then recurse down from that point on.

## Coding the algorithm
Now comes the fun part, let's translate our algorithm into code! Take a look at the algorithm I've coded below:
```cs
        private static void generatePermutations(string prefix, int leftRemaining, int rightRemaining, List<string> permutations)
        {
            //if the number of right and left parentheses remaining are both 0 we successfully genereated a valid combination
            if (leftRemaining == 0 && rightRemaining == 0)
            {
                permutations.Add(prefix);
            }
            //we place as many left parentheses as we can first, before we begin backtracking
            if (leftRemaining <= rightRemaining && leftRemaining > 0)
            {
                generatePermutations(prefix + '(', leftRemaining - 1, rightRemaining, permutations);
            }
            //we place right parentheses to close our left parentheses
            if (rightRemaining > leftRemaining && rightRemaining > 0)
            {
                generatePermutations(prefix + ')', leftRemaining, rightRemaining - 1, permutations);
            }
        }
```
Now let's look at the output:
    ((()))
    (()())
    (())()
    ()(())
    ()()()

Success!

For this algorithm instead of printing to the console I decided to store the strings inside of a list so that I can access them later, this will require more space and if all we want to do is print the combinations we can just call Console.Println(prefix) instead. Furthermore, in this case a pass the prefix along with parenthesis down to the next call. This creates a new string each time, we could improve by passing a char array and an index into its position. This would work because successive calls would overwrite existing parentheses if they were already there. Beyond that, this solution is fairly simple and not too complex, it just requires some thinking to make sure we pick the right path from the start.

## Conclusion

To end this post, I want to highlight how important to think through the problem first before diving into code. This applies to all aspects of software engineering and not just fun programming problems such as this one. By breaking down our problem into smaller subproblems we started to see a solution form and began to get ideas to solving them. Yet, it's not always easy to figure out what the subproblems are. As such, it can be useful to draw out a tree, such as the one above and understand how we go from the top of the tree to the bottom. It's also important to understand the concept of decision space. The decision space is simply the options we have at each step in our algorithm. In this case our decision space was to insert a left parenthesis or a right parenthesis. Once we have those two concepts figured out, putting them together to make up our final solution usually comes naturally. Thanks for reading and stay tuned for more!
