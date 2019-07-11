---
title: "Weekly Digest 2019-06-24"
excerpt: "Rules Engine for JS. Why Event Driven Architecture might work for you."
author_profile: false
read_time: false
categories:
  - Weekly Digest
tags:
  - rules engine
---

{% include figure image_path="http://i.imgur.com/MAzq7l2.png" alt="" caption="" %}

[Json rules engine](https://www.npmjs.com/package/json-rules-engine) is a javascript package that allows you to run custom rules in your applications. The engine will evaluate the conditions and parameters defined in a JSON format, and provide the desired output. This means that while your program is running, your application users may submit custom rules to be evaluated at runtime. 

A good use case for this package is a customer-facing business application. A hotel may decide to offer different room rates to visitors of their website, and the rules may be dynamic, based on customer value. The rules may also change at different time of the day depending on existing rooms inventory, which cannot be pre-determined and requires the revenue manager of the hotel to craft the rules. So instead of having the users learn complex programming languages (code injection is probably dangerous as well), the application could have a frontend to assist the users in crafting rules and producing the JSON file. This file will be submitted to the rules engine to be evaluated, and the application will act on the subsequent output.


{% include figure image_path="https://d9hhrg4mnvzow.cloudfront.net/try.solace.com/wp-download-eventdrivenmicroservices-lp2/de9a1190-screen-shot-2018-06-25-at-10-29-55-am_06507x06507x000000001.png" alt="" caption="" %}

Solace, a software provider that specialise in event broker platforms, had published a whitepaper that very simply but concisely explains what is an event driven architecture without going deep into the technical details. The paper will allow you to understand the paradigm of event driven systems at a high level, the problems with existing microservices observed in the current industry, and how those fundamental problems may be overcomed by adopting the new design. You may download the whitepaper for free [here](https://try.solace.com/wp-download-eventdrivenmicroservices-lp2/).

Of course, the paper only tells the good part of the story since the objective is to attract new customers and increase awareness. An event driven architecture brings its own set of problems, especially if such a system needs to be integrated with legacy systems, or existing microservices that does not manage application states in the same manner. If you want to find out more, check out this [blog post by Martin Fowler](https://martinfowler.com/eaaDev/EventSourcing.html) that goes into greater conceptual details on this topic.