

Notes from: https://landing.google.com/sre/book/chapters/introduction.html


*INTRODUCTION*

*Old model*

Developers vs Sysadmin/Devops

> "We want to launch anything, any time, without hindrance" versus "We won’t want to ever change anything in the system once it works"

> Eventually, a traditional ops-focused group scales linearly with service size:


*New model*

> SRE is what happens when you ask a software engineer to design an operations team.

> set the hiring bar so high in terms of both coding and system engineering skills means that our hiring pool is necessarily small

> Once an SRE team is in place, their potentially unorthodox approaches to service management require strong management support.

> Google caps operational work for SREs at 50% of their time. Their remaining time should be spent using their coding skills on project work.

Work that overflows the 50% cap is assigned to regular developers, and becomes motivation for regular developers to build automated systems

*Availability*

Service availability budget e.g. 99.99% per quarter means you cannot release if you go over budget. This because motivation to do code right, and create effective monitoring and roll-back features. Must have buy in from management.

*Monitoring*

Three types of monitoring:

1. Alerts - immediate action required
2. Tickets - non-immediate action required
3. Logging - no action required

MTTR - Mean time to repair system

Humans add latency - Many problems with quick-automated fixes, is better than a single problem that requires a human

Pre-planning for failure in a "playbook" reduces MTTR 3x

Exercises like "Wheel of misfortune" can also help

*Change management: ensuring deployments are safe*


*EMBRACING RISK*

- Progressive rollouts
- Quickly/accurately detect problems
- Safe rollbacks

Availability = uptime / time

Aggregate availability =  successful requests / requests

Risk tolerance: for the time being it looks like contracts would be a good start

*Motivation for error budgets*

Product developers want product velocity
SREs want reliability
Both parties need input into release rate
∴ Need a common incentive, thus error budget

Produces a nice cadence of experimentation at the start of budget, and building safety toward the end

*REDUCING TOIL*

> If a human operator needs to touch your system during normal operations, you have a bug. The definition of normal changes as your systems grow.

- Carla Geisser, Google SRE

> If we all commit to eliminate a bit of toil each week with some good engineering, we’ll steadily clean up our services, and we can shift our collective efforts to engineering for scale, architecting the next generation of services, and building cross-SRE toolchains. Let’s invent more, and toil less.

*MONITORING*

- Big job
- Google trended to simple & faster monitoring systems, with better tools for Post hoc analysis. Exception would be anomaly checking.
- Google has limited success with alarms with complicated logic

> Over the long haul, achieving a successful on-call rotation and product includes choosing to alert on symptoms or imminent real problems, adapting your targets to goals that are actually achievable, and making sure that your monitoring supports rapid diagnosis.


*SIMPLICITY*

> The price of reliability is the pursuit of the utmost simplicity.

> At the end of the day, [a SREs] job is to keep agility and stability in balance in the system

*The Virtue of boring*

> Unlike a detective story, the lack of excitement, suspense, and puzzles is actually a desirable property of source code.

- Google engineer Robert Muth

Create a clear distinction between essential (designed) vs accidental (debt) complexity

*The "Negative Lines of Code" Metric*

The lines that are _removed_ by a developer

*DATA INTEGRITY: what you read is what you want*

_So many requirements to meet_ https://landing.google.com/sre/book/chapters/data-integrity.html