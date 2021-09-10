---
layout: post
title:  "Decision Making for Architecture Design"
date: 2019-10-31
categories: logs
---

Procrastination is a common problem in architecture development initiatives. In fact, it’s a problem that dictates almost every move we make. When things are complicated it is easy to be overwhelmed and hard to get started. At the same time, no-one argues about the importance of the informed first move which gives the momentum that builds on itself.

Procrastinating (and missing) that first step is unacceptable, since most contemporary organizations born and die because of technology. In this post, I’ll recommend remedies for this problem by sharing lessons learned on prototyping, land navigation, leading an infantry platoon and MMA. Yeah, I KNOW! It sounds pretty random, but it turns out to be surprisingly important.

First, let’s establish key terminology: Architecture design is a program and a deliverable. It’s a program that is long and often without a clear end date. The deliverable side of architecture covers a long-term technological vision aimed to put complex goals and requirements in an easily understandable format. The requirements change over the time which is known as “trying to hit a moving target”. These combined create two large gaps between intention and architecture, and architecture and implementation.

That’s why, in my opinion, making decisions get complicated. The following principles have proven useful to keep things squared away:

#### ATOMIC DECISIONS

People who don’t have experience in fighting often think that an MMA fight is a flow of primal instinct. This may be the case sometimes, but I can assure you that it is not a long-term winning tactic. When you are looking your opponent eye to eye, your mind goes to overdrive. You ears are ringing from the excitement caused by adrenaline, fear and the need to act. And while it does not feel like you’re thinking, you are capable of decisions making. T

he difference is that those decisions, even responses have been made before you ever stepped on the mat. When you are rolling and practicing you have the opportunity to pause and build those rehearsed action plans. If you don’t do that you are already lost. As an example: what if the opponent throws a right hand, shall you duck and counter or shall you shoot for the legs. It should be easy to decide on the fly, right? Wrong:

*   You have to choose from the tens of techniques that you know,
*   You need to consider the counters the opponent can do,
*   You need to think of how much energy you are ready to consume,
*   You have to manage your fears and
*   You have to assess the risk of losing after your move.

It’s too much to handle. Therefore, don’t make decisions, especially the big ones, real-time. However, make small “atomic” risk-free decisions often. If possible, analyze those decisions prior the need to decide. You can faint, jab or move quite risk free and while improving your position. Feedback on these smaller decisions, even failing few of them, prepare you to make the bigger decisions that hit the target. This way you can take the hard first step without risking investing too much to it.

Sometimes when you are in the woods and don’t know exactly where, it helps to move a bit to find yourself on a map. Maybe you see a lake, a hill or a road that puts you back on the map. I find this also true when I’m in a new city relying on GPS. It does not matter if your first small move moves you away from your destination since now you know which direction your compass needle is pointing. Make small decisions and adjust when you see the result. This way the big risky decisions become manageable.

What was explained is the concept but before you can make atomic decisions you have to understand what is important, what are the decisions and what is the substance you’re considering. Let’s build that workflow next.

#### SET THE AGENDA, PRIORITIZE AND SET BACK STOPS

First step of building atomic decision making is to determine what is chronologically important. Consider this simple example: Let’s assume you should create a secure network design for a global company. You start by setting the agenda, for example, it should be a site to site VPN, centrally managed to drive down operational expenditure and follow ISO control framework to make sure the solution is credible from the get go.

Alternatively, if you find yourself raving about topics that people around you find hard to justify, there is a high risk that you are prioritizing the wrong decisions. Example red flags in this context typically are what devices are to be bought, how are you going to implement IKEv2, if that new BGP bug is going to crash the entire solution or how to deal with that one site’s salty admin.

Lack of prioritization is one of the main reasons why it is hard to start making progress on architecture development. Often this materializes on two observations: trying to commit to too big and complex decisions too soon or wrong decisions prioritized on the wrong moment (e.g. diving into implementation details directly). Here your land navigation skills come to play once more.

Ever heard of back stops? Every single time we prepared maps in Scouts or in the army we marked it with well-known back stops. Back stops are significant terrain features that tell you, you’ve gone too far from your original objective.

Even though people wiser than me had told me to use back stops ever since I was a boy, I learned the importance of them after leading my rifle platoon in the middle of a swamp on a cold, damp, dark autumn night. I still see the 35 annoyed wet, tired and dirty faces looking at me with utmost disgust and disappointment. Upon this humbling experience, I could just take my lumps and learn from them.

After that I marked my maps with one to three back stops for an objective. You need secondary and tertiary back stops to stop you from going if you’re way off the target. When you hit a back stop, you stop and try to get back to the original objective. Some years ago, I realized that you could and should use back stops in design work. Few examples below:

*   Don’t go planning physical work or designing routing if the business and functional requirements are not documented,
*   Don’t start specify security features if you don’t recognize the law or control framework that should be followed,
*   If you are already choosing tools, but can’t articulate well why they are needed,
*   If the discussion is around hype words like digitization, machine learning or IoT, but no-one knows how to make money from it,
*   And so on.

Take a pause to create a timeline of your high level priorities and make sure to implement back stops that alert you in case you are prioritizing wrong decisions or if you are going too deep too early. Once you have an understanding of the priorities your next steps are to divide those to atomic decisions and to understand the content of the decision.

#### EXPOSE THE ATOMIC DECISIONS

While prioritization is a common leadership practice, even some of the most know publications stop after that recommendation. It’s common sense to do the first thing first. Instead the problem is that you don’t know what to prioritize before you have a list of decisions to be made in front of you. To make such a list you need a hell lot of a knowledge and experience to understand how to divide your priorities to meaningful atomic decisions.

On the other hand, if you aren't able to do this you will go suggesting risky solutions and performing difficult projects and end up re-actively being notified of the decisions you should make. To go back to my MMA example this translate to as if you would try to make a decision analysis while 100 kg opponent grinds your face on the mat and tries to take your arm home with him. In short, you are too late and about to lose. Aim to think and live the decisions before taking them.

Sounds academic? It’s not. If you are inclined to business and leadership, it means you need to read case studies to learn what people before you did to solve similar problems. If you are inclined to technology, you must put in some serious lab time to understand the key decisions. These exercises help you to create the list of decisions, to rehearse most decisions in a “free-to-fail” setup and gives you time to concentrate on the ones that you didn’t know to expect.

I just recently worked on a very demanding complex use case. The problem was that I wasn’t sure if we could do it. At the same time, this use case played a significant role in the sprint’s value proposition. To avoid losing face and leading our sprint team to a cold, freezing, damp swamp of misery, I wanted to divide the decision and the faith of this use case to multiple atomic decisions to be done in a priority order. This is how it looked like:

*   We did a technical feasibility analysis for the use case, after which we decided on the potential implementations
*   I drew two primary candidates how the use case could be completed and two contingency options that partially filled customer requirements
*   We analyzed those again based on customer requirements and selected the primary option and one as a contingency option
*   We built a small lab for the primary option, to get more confidence
*   After labbing we decided to commit the primary option to the sprint
*   In addition, we decided to keep the contingency plan in place, should something go wrong in the sprint

This is how atomic decisions could look like. Take small early commitments towards the grand decision. On each step we learned more about the use case’s complexity which put us on a much better position to maneuver as the challenges were revealed. Blindly committing this use case to the sprint would’ve been a major failure from my part.

#### KNOW THE DECISION

There seems to be a myth out on the wild that architects don’t need to know how to actually do things, they just need to be able to speak the “right language”. I’ll have to call this for bullshit. In general, following this advice is a very bad idea. The more you know, the better you are able to sanity check your decisions. It is as simple as that.

The army knows this the best. Me and the brothers before me until the dawn of time, who went through the platoon leader training, were taught every single job in the platoon. Platoon leader’s job is not to shoot the machine gun, but he must understand how the system works, has experience shooting it, know how to maintain it, how to position it for the best firepower and how to attack against it. This is the caveat: you won’t be _**the best**_ expert on it, but you still can operate it if needed.

To divide your priorities to atomic goals and to make meaningful sanity checks on them you must be able to prototype your solution. Do you need to be able to build it to a fully operating production system? Not always but sometimes yes!

Without knowledge, it’s impossible to determine what decisions there may be ahead, where to start your journey and how likely your priorities can be completed. I argue that not having enough knowledge is one of the prime reasons why there is so much delay between design and implementation.

Gaining knowledge is easy: sit down, read, think, listen to critique from your peers and build prototypes - for example: in order to better understand design principles of Docker and Kubernetes, I built a swarm on a couple of Raspberry Pis that host a self-made, simple two-tier web application. Gaining knowledge shares the same misconception as working out. You don’t need fancy equipment or specialized program to be in shape. What you need is to feel sweat blinding your eyes, your lungs begging for air and iron tearing holes in your palms.

#### CONCLUSIONS

So there it is, make small decisions often and only after due assurance commit to bigger decisions, set your timeline of priorities, create back stops, understand what decisions you should make before you are in a losing position, study cases, put in time in the lab and know how to do every job in the shop but don’t try to pose as the best expert.

Although the list is not complete and I’m not a complete advisor on this field, I can guarantee a positive effect in your projects if you embrace some the lessons learned discussed in this post.

Don’t be afraid to struggle.