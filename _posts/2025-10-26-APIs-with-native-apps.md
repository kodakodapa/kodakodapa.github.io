---
layout: post
title: "APIs catering to native apps and web"
date: 2025-10-28
author: Björn
---


A challenge I've come across a few times is that of supporting both web and native apps in the same API. The difficulty mainly stems from the fact that it takes a long time to get a new app version in peoples hands, so old versions tend to exist out there for a long time. So if you need to update for example some copy, or change how a flow or particular endpoints work you can't just update the web and BE at the same time and be done with it.


## App Version Adoption Pattern
This is an illustration of how app version might be rolled out, and how many still user old versions

| Time After Release | Latest Version | Previous Version | 2 Versions Old | 3+ Versions Old |
|-------------------|----------------|------------------|----------------|-----------------|
| Week 1            | 15%            | 65%              | 15%            | 5%              |
| Week 2            | 35%            | 50%              | 10%            | 5%              |
| Week 4            | 55%            | 32%              | 8%             | 5%              |
| Week 8            | 65%            | 23%              | 7%             | 5%              |
| Week 12           | 70%            | 18%              | 7%             | 5%              |
| Week 24           | 75%            | 13%              | 7%             | 5%              |
| Week 52           | 78%            | 10%              | 7%             | 5%              |


### Key Observations

- **Slow initial uptake**: Only ~15% adopt in the first week (some users have auto-updates off, others delay intentionally)
- **Multiple versions in production**: You're typically supporting 3-4 active versions simultaneously
- **Issue's there for Android and iOS**: With different uptake dynamics

### Challenges with this dynamic
This slow version uptake, both in the very short term and in the long tail, create challenges when you want to do:

- **Syncronized feature releases**: Often you want to (dis)enable features for the product across your different clients at the same time. For example product functionality, A/B tests or copy changes.  
- **Bugfixes**: You typically want these out fast, and depending on severity you might want it out yesterday.  
- **Support for old versions**: In the example above some installed versions can be very old and the API might have added or changed functionality, but the old version of the API must still co-exist with the old version so that old apps won't crash.

## Mitigation strategies
Where I've worked some common ways to face these challenges are:   
- **BFF (Backend For Frontend)**: To handle copy changes needing fast updates a natural incliniation is to fetch these from the BE, as this is easy to change. This way of solving updates tends to expand into wishing for hybrid REST/BFF approaches.   
- **Feature flags**: Wrapping feature development, sometimes pretty big chunks, in feature flags is convenient. Then you can fallback on versions you know to work if any issues pop up. A/B with variant assignment can be used in a similar manner, and these can then be turned into feature flags as a winner is chosen. A typical problem with all of this is the technical debt as old flags are never cleaned up.  
- **Force updating**: You need this nuclear option for either very large breaking changes or for mitigating severe bugs. The idea here is that the app checks against allowed versions on startup, if its version is on the disallowed list the app displays an update pop-up and doesn't start. This pretty annoying for the users and should be used sparingly.
- **Expedited reviews and accelerated rollout**: At least on iOS you can ask for expedited reviews to speed up the process. These request are limited however. Also when an app is released it typically rolls out to a 100% (that are allowed to update) over a week or so, but this rollout speed can also be accelerated. This is a good way to handle medium sized bugs.
- **Versioned API**: As breaking changes, to accomodate new/changed functionality, are introduced in existing API endpoints these are made available under new versions. As an example, adding new types of offers under `v1/offers` that aren't support in all app version would be solved by keeping `v1/offers` functionality while creating `v2/offers` for the new app versions. 

## Impact on API design
There are some immediate impacts and some more subtle ones. 

An obvious but tricky one is that of finding a good balance between keeping a clean REST API design and providing pragmatic BFF functionality. It's in the interest of everyone that some things can be immediately changed easily and BFF functionality is the way you do that. I think there's no right way of doing this as the needs can differ so much from product to product, and from one part of the product to another. I think the key is to recognize this tension, acknowledge the needs from both sides and try to make smart choices - maybe more BFF in some parts where big changes are expected, and no BFF and clean REST in other parts. Don't expect it to be a perfect balance, that your choices will be perfect  and that the code will be perfectly clean, and that's OK.

Another one is keeping lots of versions of the API, and keep supporting old functionality. From the point of view of REST API:s this isn't a problem though, adding a versioning is clean and reasonable. When it comes to the BE it's a more traditional topic of managing complexity, making sure things work (add test) and keeping the code clean - but that's another topic.

Yet another is that of being proactive. If you're designing an API with app clients you need to enable versioning on your API from the start. Also when you're building features in the apps and designing the API try to think of the future where there will be a bunch of old apps using a certain functionality - maybe you'll find that's it's motivated to add a killswitch for the functionality right from the start, e.g. don't show it if the response is empty or gracefully handle errors from the endpoint in the apps.