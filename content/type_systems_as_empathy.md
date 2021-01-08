+++
title = "Type Systems as an Expression of Empathy"
date = 2021-01-24

description = "Thinking about type systems from the perspective of empathy for the people affected by your programs."

[taxonomies]
categories = ["Type Systems"]
tags = ["empathy", "types"]
+++

Personally, I've come to prefer languages that have static type systems over those with more dynamic ones. I was reflecting while investigating a stack trace for work and I realized that there is an undercurrent of empathy to my preference. Much has been said about "types as tests" and "eliminating classes of bugs by making illegal state unrepresentable", but I haven't seen it expressed from this framing before.

<!-- more -->

This is definitely not to say that we should generalize this idea into "dynamic typed languages implying a lack of empathy" or "all static languages having empathy as a core value". Further down, I will try to discuss other approaches in software development that can be seen as ways of expressing empathy.

## First, what do I mean by types expressing empathy?

When I think about empathy in the context of software development, I think of the end users of the software I write, of the operations people that will support it, and of the future developers who will modify it. I want my users to be able to accomplish their goals in as smooth of an experience as possible. I want my operations teammates to be able to spend their time working to improve their processes instead of diagnosing and mitigating issues caused by my software. I want future developers to be able to understand the context around code that I have written so that they can be equipped to change it as needed in the future.

### So how do types influence that?

Many of the discussions that I've observed about static v. dynamic types focus on individual developer experiences. The common complaints about stricter type systems are about "fighting the compiler" and "prototyping more slowly" compared to dynamic type systems.

When we view a program in its whole context, we see that there are more considerations than solely the experience of the initial developer. I'm of the opinion that it is possible to do more work up-front in order to require less work for the whole team over the entire lifetime of an application. (This a heuristic rather than a rule. It is possible to spend too much time building a program that solves the wrong problem. The problem can also change over time and require large amounts of effort later in its life.)

Given those caveats, what role do types play in the up-front effort? One aspect of this role is moving assumptions about data to a central location in the codebase. Given an example `User`, I'll show what I mean.

```rust
pub struct User {
  pub username: String,
  pub email: Option<String>,
}
```

The developer reading this code does not need to know the details of how users are registered and managed to understand that a user might not have an email associated with their account. When adding a feature to the application to send users email invoices, the developer would know that they would need to handle a case where the user was redirected to add an email before the invoice was sent. With types codifying our email constraint, future developers are prepared if the user system changes such that a user will always have an email. The type-checker will show them each place where changes are needed to manage a missing email and simplify the logic.

Without that information, the developer would need to rely on documentation that might be out of date, other uses of the data that might not have the same concerns about missing emails, or sources outside of the codebase such as undocumented knowledge from teammates. If these sources of knowledge didn't tip off the developer, an assumption that a user will always have an email could cause a production bug. When the implementation changes in the future, the developer might miss the removal of the logic that handles users without emails. This would further confuse future developers that found places in the codebase that assumed an email would always be present and also places where an email is assumed to be possibly absent. Over time, the amount of work done and the number of people impacted is likely to be significantly higher with this approach compared to the approach with types.

## Other Approaches

Types alone should not be the whole approach to doing more work up-front in order for the team to do less work over time. A team should use several overlapping approaches to meet this goal. The languages and tools they are using likely already have techniques and integrated tools that can be used.

Well-written and carefully maintained documentation can be one way to serve this purpose. In our example, if the source of our `User` was from an internal API, an API documentation tool such as [OpenAPI](https://www.openapis.org/) could convey that information to a developer. This documentation would need to be owned by the team that managed the API. They would need a well-defined versioning scheme for API changes and would need to be able to communicate directly with their API's users to notify them of upcoming releases.

If the user came from an internal library, the team that authored the library could provide a test factory using a tool such as [factory_bot](https://github.com/thoughtbot/factory_bot) that generates valid users. This would make it more likely that an invalid assumption about the email would be caught via tests before it made it to production. The library team would need to provide example tests or collaborate with the users of the library to ensure the factory would be used correctly where it was needed.

A more abstract approach is to have developers do support rotations so they gain appreciation for the amount of effort required to resolve production issues. This won't retroactively make their code more robust, but it might be something that sticks with them for the next project or feature they implement.

## So what's the takeaway?

You should care about your team and your users. To me, that is an inherent value. Even if it is not one of yours, we work in a collaborative industry and neglecting your teammates will eventually catch up with you (see the [common perception](https://twitter.com/cassidoo/status/1150170262228201472) of [10x developers](https://twitter.com/mipsytipsy/status/1151436649714216960)). Likewise, neglecting your users won't cause you immediate problems, but you will develop a negative reputation (see Google's practice of [sunsetting applications](https://killedbygoogle.com/) and [often replacing them with multiple, redundant ones](https://twitter.com/patio11/status/1321310132106649601)).

It is very challenging to quantify metrics that would indicate problems with these relationships and so they are often neglected in favor of metrics that can be easily measured to observe progress over time. The problems occur inside the larger systems of the team and the company, so you need to become familiar with those systems in order to be successful at addressing them. Learn how your coworkers in different roles work, what blocks their productivity, what frustrates them, and what makes them feel successful. Then, think about how you can use your tools and techniques to gradually improve the systems you've found and empower your teammates.

## References and Further Reading

1. [Blogging principles I use](https://jvns.ca/blog/2017/03/20/blogging-principles/#be-positive) by Julia Evans
1. [No, dynamic type systems are not inherently more open](https://lexi-lambda.github.io/blog/2020/01/19/no-dynamic-type-systems-are-not-inherently-more-open/) by Alexis King
