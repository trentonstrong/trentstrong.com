---
layout: post
title: SQLite database engine: Code review and exploration
tags:
	- sqlite
	- database
    - code review
    - programming
---

In this post and a series to follow, I will attempt to dissect the SQLite engine component by component.  The intent behind this little project is manifold:

* I am fascinated by database engines and their implementations.  The problems solved cross a wide berth of software engineering including language parsing/interpreting, complex data structure design, and low-level interaction with operating system components such as memory management.

* I cut my teeth on C (or C-flavored C++), but I am getting pretty rusty and would like to keep those synapses firing.  Structured code reading is a practice that takes some discipline but I think has a high payout relative to the amount of time spent, especially if the code is approachable and "good".  A lot of database engines (RDBMSs and non-relational types) are written in low-level languages to exercise control over memory management and operating system interaction in order to provide consistent performance or make guarantees on data persistence and operations (a la ACIDity).

* Others might enjoy reading this, who are perhaps newly interested in some of these topics or are experts far beyond my modest perspective.  I would love feedback from either!

Why SQLite?
===========

A