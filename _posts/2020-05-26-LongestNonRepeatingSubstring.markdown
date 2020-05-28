---
layout: single
title:  "Longest Substring Without Repeating Characters"
date:   2020-05-26 12:00:00 -0400
classes: wide
categories: strings
---

## A non-repeating substring
Our problem, which can be found [here](https://leetcode.com/problems/longest-substring-without-repeating-characters/submissions/), tasks us with finding the longest substring in a string that contains only non-repeating (unique) characters. A substring is a continuous sequence of characters from the original string. For example: “abc” is a substring of “abcd”, but “acd” is not. At first glance this problem seems simple, and it is! At least it is simple to come up with a O(N^3) solution. It's going to take a bit more effort however to come up with an efficient, optimized solution that runs in O(N). Let's take a look and see how it's done.

## First Recursive solution

As always, let's begin with a simple recursive solution that takes a brute force approach to the problem. This brute force will try every possible substring of a string to find the longest substring with only unique characters. For example, if we had the string "abc" we would try string "a", "ab", "abc", "b", "bc", and "c". We generate these combinations by maintaining an index to the start and end of the substring, generating all these possibilities takes O(N^2) time. It also takes O(N) time to verify if the substring is unique, because in the worst case we have to look at each character in the string to verify its uniqueness. Therefore, we have a O(N^2 * N) => O(N^3) solution. Let's take a look at the code below:
```cs
    public int LengthOfLongestSubstring(string s) {
        //if our string is length 0 or 1 then it will contain only unique characters
        //the longest substring is the length of the string
        if(s.Length < 2){
            return s.Length;
        }
        //the maximum substring we found so far
        int max = 0;
        for(int start = 0; start < s.Length - 1; start++){
            for(int end = start; end < s.Length; end++){
                if(IsUnique(s.Substring(start, end - start + 1))){
                    max = Math.Max(max, end - start + 1);
                }
            }
        }
        return max;
    }

    private bool IsUnique(string s){
        //we initialize the array to the size of the extended ASCII char set
        int[] freq = new int[256];
        foreach(char c in s){
            freq[c]++;
            // if a character appears twice, we know it's not unique, return false
            if(freq[c] >= 2){
                return false;
            }
        }
        return true;
    }
```
The IsUnique(string s) receives as input a string s. The array freq counts the number of times each character appears. Each character has an integer value associated with it, for example 'A' is 65 and 'Z' is 90. We initialize its size to 256 since this is the size of the extended ASCII character set. If we wanted to handle a larger char set such as unicode we would better off using a dictionary, and checking if the character already exists in the dictionary. If we try to run this implementation on LeetCode we will get a "runtime exceed" message, since it is simply too slow to handle large inputs.

## Optimized brute force solution

**Optimized brute force** might sound like an oxymoron but it proves useful here, where we take advantage of certain facts to improve our run time by a considerable amount. Our first improvement is that we will add an 'else' statement after our call to IsUnique. If the case arises where the current substring we are looking at is non unique then we will break and increase the start index. This works because if the substring from start to end is not unique, then the substring from start to end + 1 is not unique either, since (start -> end + 1) contains the substring from start to end. With this improvement we can actually pass the test! Albeit with a very slow run time of 1292ms.

For the next improvement take notice of the fact that we are searching for the longest possible unique substring. Using this fact, we can see that's pointless to check if a substring that is shorter in length than our maximum substring is unique. Since, it cannot be the longest possible substring we continue increasing the end bound until we get substring that is longer than our current maximum. Only then do we verify if that is unique. Doing this improvement saves a lot of processing power, even though it doesn't reduce the complexity from O(N^3) this time we pass the test with a runtime of 132ms, quite a bit better! Take a look at the code below:
```cs
public class Solution {
    public int LengthOfLongestSubstring(string s) {
        if(s.Length < 2){
            return s.Length;
        }
        int max = 0;
        for(int start = 0; start < s.Length - 1; start++){
            for(int end = start; end < s.Length; end++){
                if(end - start + 1 < max){
                    continue;
                }
                if(IsUnique(s.Substring(start, end - start + 1))){
                    max = Math.Max(max, end - start + 1);
                }else{
                    break;
                }
            }
        }
        return max;
    }
    
    private bool IsUnique(string s){
        int[] freq = new int[256];
        foreach(char c in s){
            freq[c]++;
            if(freq[c] >= 2){
                return false;
            }
        }
        return true;
    }
}
```
## Optimal Sliding Window approach
The technique that leads us to our optimal solution is called the sliding window technique. The sliding window works by maintaining a pointer to the start and end of our window. If we find a non unique character in our window then we shrink our window until the condition is restored. If we find a unique character then we can increase our window. The "window" is essentially the current chunk of the string we are looking at. If we have a string "abbcef" and our current chunk is "bc", then we can make our window bigger by 1 and include it in our substring. Take a look at the photos below if you're having trouble visualizing this:
![Iteration Table]({{site.baseurl}}/assets/Images/LNRS/p-1.jpg)

![Demonstration of execution]({{site.baseurl}}/assets/Images/LNRS/p-2.jpg)

When we encounter a character we've already seen we need to shrink our window until it contains only unique characters again. We find the second 'b' in our string "abbcef" we shrink our window until the second 'b', since it wouldn't be possible to make a longer substring out of "abb", as it contains 2 b's.

The code below is our optimal solution which runs in O(N) time:

```cs
    public int LengthOfLongestSubstring(string s) {
        if(s.Length < 2){
            return s.Length;
        }
        //the dictionary keeps track of characters we've already seen
        Dictionary<int,int> substring = new Dictionary<int,int>();
        //the left bound of our window
        int left = 0;
        //the right bound of our window
        int right = 0;
        int max = 0;
        while(left < s.Length && right < s.Length){
            //if the next character is unique add it to the dictionary and increase our window by 1
            if(!substring.ContainsKey(s[right])){
                substring.Add(s[right],right);
                right++;
                max = Math.Max(max,right-left);
            }else{
                //if the character was already seen we need to shrink our window until we have all unique characters
                substring.Remove(s[left]);
                left++;
            }
        }
        return max;
    }
```
The great thing about dictionaries is that operations such as Add and Remove run in constant time, therefore they don't add to our time complexity. Dictionaries operate by storing key-value pairs. The key is used to access a certain value in the dictionary, and in C# these keys must be unique. We add a key by call Add(key,value) on our dictionary. For this problem we don't care about the value, since we are only using the dictionary to tell us if we've seen a certain character, which is accomplished by calling ContainsKey(). If you are using another programming language such as Java you would be using a Map or a HashSet.

## Conclusion
I'm trying to experiment with doing 1 or 2 longer posts a week along with a couple shorter posts, such as this one. Let me know your thoughts on this format and if you like these short write-ups that tackle specific problems.

I hope you learned something by reading this article and that the I've helped demystify the sliding window pattern. The sliding window pattern comes up often and in many problems, so it's useful to know how it works. String problems in general take advantage of this because a string is simply an array of characters, terminated by '/0'. Stay tuned for more content and have a good day!
