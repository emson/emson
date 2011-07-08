--- 
layout: post
title: IManage error - The semaphore is set and cannot be closed
wordpress_id: 4
wordpress_url: http://blog.emson.co.uk/?p=4
---
#IManage error - The semaphore is set and cannot be closed
Using the Interwoven IManage API, I was trying to create a connection to the IManage server and kept getting this error message:

	The semaphore is set and cannot be closed.

It turns out that this error message is misleading and can hide a multitude of symptoms.  In this case it was returned because the system cannot create a connection to the server, as I had accidentally put in an incorrect server name into my connection method.

I did however do some research into what a **semaphore** actually is.
The choice of word comes from a time when flags were used to signal telegraph messages.

However the [Dictionary](http://dictionary.reference.com/browse/semaphore) computer definition of a semaphore is:

<blockquote>semaphores are a software mechanism to regulate process synchronization, proposed by Edsger W. Dijkstra.</blockquote>

[Wikipedia](http://en.wikipedia.org/wiki/Semaphore_%28programming%29) gives a more detailed explaination of a **semaphore**, see below:

<blockquote>A semaphore, in computer science, is a protected variable (an entity storing a value) or abstract data type (an entity grouping several variables that may or may not be numerical) which constitutes the classic method for restricting access to shared resources, such as shared memory, in a multiprogramming environment (a system where several programs may be executing, or taking turns to execute, at once). Semaphores exist in many variants, though usually the term refers to a counting semaphore, since a binary semaphore is better known as a mutex. A counting semaphore is a counter for a set of available resources, rather than a locked/unlocked flag of a single resource. It was invented by Edsger Dijkstra. Semaphores are the classic solution to preventing race conditions in the dining philosophers problem, although they do not prevent resource deadlocks.</blockquote>

