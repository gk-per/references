# 4 Day Bug Postmortem

So, you just finished fixing a bug (or series of bugs, one right after another) in an attempt to get your service (or series of interconnected services) back working to its fullest capacity. You've had the series of "I've done it. I fixed the problem!" eureka moments, followed immediately by checking the UI or the logs and seeing a new bug, or worse, seeing the exact same behavior or error message again and again.  This dance has been going on for the better part of four days, taking up several hours of each day and just generally being a thorn in your side. But it seems to be over. The logs look good, the UI is behaving as expected and you're relieved that you've made it work.

Let's talk about what went right, what went wrong, and how we can avoid some of the headaches that contributed to this whole situation.

## What Went Right

There are a few guaranteed "what went right" bullet points once you're past the point of fixing a bug and getting it sufficiently tested:

* The bug is fixed, and your new feature or your efficiency refactor or whatever that caused the bug is now part of the shippable product.
* You learned something, whether it was from searching on Google, asking for help from someone who knows better, etc.

But there are probably some other examples you can think of that relate to some of these anecdotes I'm able to share from my own personal 4 day bug:

* I got extremely familiar with our dev environment's logs and learned what terms to filter by the find the errors and what the state of background logging is in our application.
* My bug involved several different services communicating with each other, and requests/responses between them. I am not the original author of these particular services, so I am a lot more familiar with the inner workings of them after all the time spent debugging them.
* I was able to perform several simple refactorings-on-the-go, in terms of things like adding needed comments, removing unnecessary lines, and generally trying to leave the code cleaner than I found it.

## What Went Wrong

Let's jump into the good stuff, though. What went wrong? How do you look back at the hours you've spent over the past week and justify not forseeing the additional bugs that popped up after bug fix number one, or two, or three?

* Requirements changed. This feature you're working on probably has some past features as dependencies and there will be features in the future that depend on the code you're writing today. Requirements and acceptance criteria are dynamic, and what you're working on in the middle of the sprint might change. Hopefully not too much. But in the case of my bug, part of the feature that the initial bug stemmed from was modified during the course of testing that led to the bug being created. So now, in addition to fixing a bug (and whatever bugs may or may not result from the initial fix) you have to change the feature itself and retest that. 
* Although I have gained a good bit a familiarity with the logs, this has led me to the conclusion that the background level of logging is quite verbose. It's hard to navigate them without knowing exactly what terms I'm searching for and even then, they move far too quickly to consider following in real time.
* There are no unit tests. Yeah, that's right, you head me. No unit tests. And this is a standard set of microservices in your cookie-cutter C-style language.  The original team of implementers didn't believe in unit tests and so it's just not a thing. 

## What Did We Learn

Wait a minute, no unit tests? How do you test, one might ask? The answer to that, is to just assume that your dev environment _will_ occassionally be broken, and merge PR reviewed changes into dev, and smoke test directly in dev. Chaotic? Potentially. In our case, the services are not that badly designed and most of the other services usually work pretty well even if you happen to brick one for a while. It does take communication and understanding across the team to be courteous enough for this to work.

What can we learn from the other 'What Went Wrong' bullets? For one, it's always worth considering what level of background logging you need, especially on an environment basis. [{!!Add more about this here, I'm not sure what best practice is tbh but it was difficult to sift through such verbose logs and I'm sure they take up a lot of storage space!!}]

In the case of changing requirements, this experience has reaffirmed to me that it's all good to me personally when features or requirements change. It's not my favorite thing to have to change something mid-testing, but if it makes the software better I think I would rather spend the extra time to get it done while the topic is still fresh on my mind. As long as everyone on the team is understanding that every bit of added complexity will add to the timeline of when features can be shipped. The more dynamic you allow your acceptance criteria to be, expect flexibility on timelines.
