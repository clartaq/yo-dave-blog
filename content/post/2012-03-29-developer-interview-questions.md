---
title: Developer Interview Questions
tags:
  - interviewing
id: 539
comment: false
categories:
  - programming
date: 2012-03-29 08:48:17
---

Picking the right people to expand an organization is one of the most important things you can do. In my career, I've participated in many, many interviews on both sides of the desk. As a scientist and manager, my interviews have been very different from those being proposed for hiring developers. From that perspective, there seem to be two categories of questions that are popular right now that seem to be pointless, potentially embarrassing, or outright business risks. Let's consider those categories and examples and what might be a better way to get the information you want.

# "College Course Trivia" Questions #

These are questions that go back to some of the fundamental skills and concepts that a candidate should have learned in school. Some of the most justifiable, in my opinion, are intended to find out if the candidate can actually program at all. For example:

**Interviewer**: Write a "Hello World" program on the whiteboard.

**Candidate**:

```bash
    10 PRINT "Hello World"
```

What does anyone learn from the answer?

Or you might have a candidate who knows the subject better than you. After you've given the candidate a tour of your facilities:

**Millennial Interviewer**: Write a "Hello World" program on the whiteboard.

**Baby Boomer Candidate**: Sure. I'll do in it in FORTRAN. What's the LUN for the chain printer I saw in IT?

**Interviewer**: What's an LUN and a chainprinter?

The candidate flips the bozo bit on the interviewer and proceeds to explain.

Or maybe a Java Candidate wants to get a little fancy for this boring question:

**Interviewer**: Write a "Hello World" program on the whiteboard.

**Candidate**: Sure. Here's a graphical version using the raw AWT.

Although I've been writing Java/Swing programs for more than 10 years, I had to do a little research to be sure I'd recognize a correct answer to this question. Of course, I know how to launch a Swing program without messing up the EDT, but I wasn't sure if any such precautions were needed for AWT. Sure I could check the answer after the interview but would have lost the opportunity for intelligent discussion of the answer with the candidate.

Or maybe you're a Lisp shop interviewing someone familiar with a different dialect:

**Interviewer**: Show me how to reverse a list.

**Scheme Candidate**:

```scheme
    (define (rev2 lst)
        (foldr cons '() lst))
```

**CL or Clojure Interviewer**: Oh, good. He knows to use efficient library functions to do the work rather than rolling his own. OR Doesn't he know how to manipulate the nodes in a list on his own?

**Scheme Interviewer**: Oh, that's wrong. Does he really think `foldr` is the correct function or did he just get it confused with `foldl`?

Three very different responses to an almost correct answer. How will they vote in your interview panel? What's the response that you think is appropriate?

There is a tendency to oversimplify these questions because they are "so simple". As a result, responses are often meaningless.

The biggest problem though is that they are usually unrelated to the job you are hiring for. Why not just ask questions directly related to the area where you want work done?

# "Do Some of My Work" Questions #

These questions are usually non-trivial and ask the candidate to come up with a solution to a problem the team is actually working on. The candidate is often given a significant amount of time to work on the problem, often on the order of days. It can represent a real hardship for the candidate.

There are two main problems with these types of questions.

### Suboptimal Solutions ###

This is by far the largest category of response. Although the problem may be directly related to the work the candidate would carry out, they just don't have enough context at the time the problem is composed and they can't get it while working on the problem. Also the conditions under which they are working on the problem are probably not similar to what they would be on the actual job. Do you do "water cooler consultations"? Code reviews? Pair programming? As a result, they may come up with a solution, but it is not useful in general.

I've often heard this type of question justified by some statement like "I just want to see how their mind works."

Well, that won't happen. I can't infer how someone's mind works from such an answer and neither can you. _Real psychologists_ can't do it either from this type of question and a single response. It's a research project, not an interview question.

If you know how certain psychological characteristics map to success in your organization (do you? really?) do real psychological testing.

A more useful line of questioning might involve their problem solving _process_. Do they have one at all? How sophisticated is it? How does it handle contingencies? And so on.

### Great Solutions from People You Can't Hire ###

This is even worse. On the rare occasion when a candidate comes up with a _better_ solution than your team, what happens if you can't hire the candidate for some other reason like poor cultural fit, failed drug tests, farts in elevators, wears suits, whatever?

You can't use their solution and not hire them. That's just wrong. As a result, you can't use a superior solution because of a stupid interview question.

Even if you tried to take the high road by signing the candidate up as a paid consultant for the duration of their work on the problem, using their solution without hiring them is at best slimy.

# Behavioral-Based Questions #

Instead of playing games to help you guess the internal operations of someone's mind, why not just ask them about accomplishments? Something along the lines of:

"Describe a case where you had to develop an algorithm that could not be handled adequately by a pre-existing library function."

"Tell me about a time when your team could not align on a goal. How was that situation resolved? How did you contribute to the solution?"

"Can you give me an example of when you showed integrity even in the face of opposition from your team?"

You get the general idea. Good follow up questions include:

*   "What was the result?"
*   "What did you learn?"
*   "How have you applied those lessons?"
*   "Which of your references can verify that?"

Getting objective evidence of performance is always better than asking someone to solve trivial or novel problems under conditions that wouldn't apply in the actual job.