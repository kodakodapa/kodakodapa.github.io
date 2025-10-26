---
layout: post
title: "APIs catering to native apps and web"
date: 2025-10-26
author: Björn
---


A challenge I've come across a few times is that of supporting both web and native apps in the same API. The difficulty mainly stems from the fact that it takes a long time to get a new app version in peoples hands, so old versions tend to exist out there for a long time. So if you need to update for example some copy, or change how a flow or particular endpoints work you can't just update the web and BE at the same time and be done with it.


## App Version Adoption Pattern
This is an illustration of how app version might be rolled out, and how many stil user old versions

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
- **Long tail**: Even after a year, ~20% of users are on old versions
- **Never reaches 100%**: There's always a persistent minority on old versions (abandoned devices, enterprise restrictions, users who refuse updates)
- **Multiple versions in production**: You're typically supporting 3-4 active versions simultaneously

This is why API backward compatibility and server-side control matter—you can't force updates, especially on iOS where review times add extra delay.


WIP