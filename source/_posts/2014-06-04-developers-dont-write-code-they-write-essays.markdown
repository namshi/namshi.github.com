---
layout: post
title: "Developers don't write code, they write essays"
date: 2014-06-04 21:07
comments: true
categories: [software development]
author: Alessandro Nadalin
---

I am always amazed by the quantity of errors I make,
on a daily basis, when writing some code: it's true that's
not my **primary** job, but at the same
time it always feels painful to find bugs or edge cases.

But one of the things I'm usually good at it's trying
to keep things as universal, simple and self-explanatory
as possible, as I've learnt, so far, that the worst enemy
of my code is **me in 6 months**.

<!-- more -->

Today, for example, I was discussing with [Lucio](/blog/2014/05/26/welcome-lucio/)
how to handle a list of errors: suppose that the user
performs an action that generates a bunch of errors, and that
you have an error handler that needs to deal with all of them.

The error handler might receive a list of these errors, process
them one by one and then let other guys take the stage: a common
solution would be to feed the handler the list of errors and let it
pop them from the list as soon as he's  *handled* them.

``` ruby Some pseudo code
class ErrorHandler {
	property queue;

	def addError (error) {
		queue.push(error);
	}

	def handle () {
		foreach (error in queue) {
			doStuff(error);
		}
	}
}
```

Nothing exceptional with the pseudo-code above, but we were wondering
(as our piece of code is a bit more complex) how to name the list of
errors that the handler deals with to make it a bit more declarative.

Since the error handler acts as a [FIFO](http://en.wikipedia.org/wiki/FIFO)
it felt natural, for Lucio, call this a **queue**. I suggested **stack**
but upon thinking about it we agreed that since a stack act as
[LIFO](http://en.wikipedia.org/wiki/LIFO_(computing) it wasn't the best idea.

[Things like this](http://www.cs.cmu.edu/~adamchik/15-121/lectures/Stacks%20and%20Queues/Stacks%20and%20Queues.html) might sound silly, but they **extremely important**
when you work with other ten people on a complex domain that
you are trying to keep as simple (and real) as possible.

We often forget that translating concepts into working code is our
main duty, and by doing so we need to keep things as clear / clean as possible:
how many times did you hear *"Oh that guy wrote it, he knows how it works, ask him"*?
I cry everytime I hear that.

When you are assigned a task, first think of the simplest and most exhaustive
solution, then write the code: your job is not measured by lines of code, but by
working software, which means maintainable, decoupled, easy to understand, (ultimately)
beautiful code.

What is the difference between an essay and some code? Ideally there is none:

* you explain / implement a concept / feature
* your artifact needs to be understandable
* it targets 2 audiences
	* a technical one, which needs to be able to understand it in depth
	* a more general one, which needs to grasp the main concepts

Actually, there is **one** difference between essays and software development: the
language they're written in.

But they all need to be self-explanatory, they
all need to nail the problem without the need to dig too deep into
the explanation, they all need to get straight to the point, making
things explicit rather than implicit.

Keep writing essays, no matter if in C, JavaScript or English.