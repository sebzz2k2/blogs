---
title: "Build your own key value store"
datePublished: Sun Apr 06 2025 18:30:52 GMT+0000 (Coordinated Universal Time)
cuid: cm95zadg0000809jvbt7scwm6
slug: build-your-own-key-value-store
tags: programming-blogs, databases

---

A key value store is a kind of NoSQL database that stores data in key value format. The key is a unique identifier. This type of data model is commonly used for caching hence making Redis a good example. Other examples includes Rocks DB, Level DB. This is just an outline of my thinking process while building one. I got this idea from the book called Designing Data Intensive Applications.

Building a real key-value store is a hard problem, but like any complex system, it can be broken down into simpler parts. I’ve been fascinated by database systems for a while and always wanted to build one. The idea felt out of reach, though, until I decided to start with the most basic version possible and build up from there. This post walks through how to build that minimal starting point.

I want my key-value store to have the following features:

1. The data should be available across restarts which means that we will be writing the contents to a file.
    
2. Since we will be writing to a file, we need to decide on a format that allows both the reader and writer to understand a common language.
    
3. The key value store should support basic indexes.
    

An append-only file (AOF) is a type of file where data is only added to the end. Once data is written it cannot be deleted or edited. This makes it simple as we don’t need to worry about restructuring the file while performing editing. Incase the system crashes while writing we can ignore the corrupt data. The storage solutions like HDDs or SDD are often optimised for sequential reads and writes.

As for the format is concerned let us choose a simple format for a key value pair — A key value pair where `:` is the delimiter and `\\n` is used to denote end of value.

`<Key><delimiter><value><\\n>`

Even though the proposed solution is simple, it is not without its own problems.

1. What if someone wants to save `:` or `\\n` in the key or value. Now the client needs to handle the escaping logic.
    
2. And what if someone wants to delete? We cannot delete a record as this is a append only file.
    

Given that engineering is all about tradeoffs I am well aware that the client can add custom logic and get over these problems easily. Well, here we would be trading off a bit of usability for simplicity, which might not be a good idea in the real world but works for us.

Now we have simplified the problem from writing a key value to store to writing two functions that will read a file that is in specific format and return the value and another function that will write to a file in a certain value.

To get a value for a key, we just need to write a function that reads from a file. Sounds simple, right? It is! There are a few things that might be worth remembering. As we are writing to an append-only file, there is no way for the program to know if there exist duplicate keys—and if there are duplicate keys, what should we return? Thinking back, an append-only file might not have been a really good idea, right? If there are multiple values for the same key, it just means the user tried to edit the key. Oh wait, if the user tried to edit the value, we don’t really care about the previous value. We can just show the user the latest value. Now, as we are using an append-only file, it makes it easier for us. Read the file from the end, and when the program encounters the key, return the value. Append-only file to the rescue.

Writing is even easier just encode the key and value in the above format and write it to the end of the file.

Your basic key value store is ready. You started using it. You now have millions of key values. You notice that some key lookups are slow while others are very fast. This is not a good experience for the user and we are asked to debug the issue. Computers are not magic there would be some logic behind any issues you are facing. Now we realise that the newer keys are faster to read and the older keys are slower to read. Why you might ask. This lies in how we read the value. If you remember we read the file from the end, and values in the last are newer values. Now what?

We add index. Just like any book has index we are going to build a index for our key value store. Now we need to choose the right data structure for building the index. Which data structure is most similarity to a key value store? A hashmap. This data structure provides O(1) lookup time. For people who don’t understand what O(1) means, it just means it's fast and the speed doesn’t grow with the size of the data. Making it a ideal candidate for this scenario.

Now we need to modify the get and set functions. While setting a value we also need to update the index with the key and a value as an offset. An offset just tells you where should you start looking for the value rather than looking through the entire file. Now to get a value check the index and jump to the position that the index recommends.

Can you see the problem with the solution? The hashmap will be empty if the key-value store restarts. Thats easy just rebuild the index from scratch every time the system restarts and we are good to go. But there is a small problem now: the start time of the database will be slower. That is again a tradeoff we are willing to take for now.

But hey… we’re not done yet. This was the simplest possible key-value store — and we made it work! But real-world databases aren’t this chill. So let’s end this blog with a few juicy questions to leave you thinking:

* What is a database that doesn’t even offer full CRUD functionality out of the box? And yet, people still use it?
    
* What if the user wants to store the delimiter in the key or value itself? Uh oh...
    
* What happens when the index becomes so large that it no longer fits in memory? Can we still do O(1) lookups?
    
* What if the file becomes so massive that it doesn’t fit on a single disk? What then? Sharding? Splitting? Crying?
    

Ok bie.