# Jenkins vs Gitlab-ci

## Intro

Researching what a engineering team looks like with Jenkins, or with Gitlab-ci (and possibly Jenkins AND Gitlab-ci).


## Notes

> The jenkins-can-do list for me is:
1. ~~Jenkins offers me timed builds, which has many use cases, which Gitlab's CI feature set currently can not cover. But Gitlab does have manual/optional steps.~~
2. ~~Jenkins offers me an ability to check stuff out of subversion, and other non-git sources. Projects for teams that I have not managed to convince to move to Git still need Jenkins for CI.~~
3. For organizations that want to create devops, and CI infrastructure elements, jenkins masters and runners are extremely easy to deploy even for dev-ops purposes where no git repository trigger is available...
_Trivial to setup a CRON_
4. For some multi-platform projects where the same git repo has to be built with different build automation steps, Jenkins retains some use cases where Gitlab-CI would be difficult to use...
5. Parameterized builds. Jenkins jobs can do some tricks with parameters that you can't do with Gitlab. Do one job twice, produce 32 bit and 64 bit binaries...
_Is YML achores to DRY-up config, but no real way to add logic. Good/Bad?_

> Where Gitlab-CI is objectively better and should REPLACE Jenkins-CI without delay in every company currently using Jenkins, regardless of which builds Ruby faster is:
1. You want a per-branch and per-fork build without any configuration, with no hassle, no fuss, it just works!...
2. You want a nice build instruction set to be in a standard place in your project (.gitlab-ci.yml) and that defines your build instructions, and these are versioned together with your code inside git, instead of separately...
_Jenkinsfile is just the same_
3. Jenkins can't mark the per-fork Merge Requests you have automatically as red or green...
4. You want a containerized-build-system? Gitlab-CI has a Docker runner. Sure you can install Docker and use Jenkins with Docker, but the gitlab-CI docker stuff is integrated, and that's just BETTER

- https://gitlab.com/gitlab-com/blog-posts/issues/278#note_17538274


> I'm being forced to use jenkins 2 at present, and I've got a few points which I thought might be useful - my opinions only.
1. Jenkins 2 has Jenkinsfile support (similar to .gitlab-ci.yml), pipelines and a plugin that supports following github/bitbucket/gitlab? projects and auto-managing jobs for jenkinsfiles discovered. Which mostly negates several of the points re using gitlab over jenkins. I mention this, because otherwise I imagine someone else will...
2. Stating that you can (in jenkins) use per job parameters outside of code is, imo, a bad thing and completely defeats the point of having build steps defined in code. gitlab wins because it forces you to follow proper gitflow. ...environment variables) in the relevant stage.
3. jenkins has docker plugins, but most of them are shit, because they force workspace/uid/gid into the container...
4. ~~on the flipside, Jenkins has ec2 plugin support for automatically spinning up temp slaves, gitlab does not, so there's more work required to set up an auto scaling group which would auto register.~~
5. ~~travis and jenkins (with plugin) have concurrency support~~
All of that might sound like I like jenkins, quite the opposite, just playing devils advocate - *I loath jenkins*.
- https://gitlab.com/gitlab-com/blog-posts/issues/278#note_17984866

> I do not actually loathe Jenkins at all. I like it a lot. It is what it is. But when you get how much setup Gitlab CI removes from you, you will love Gitlab CI even more than you might currently.
- https://gitlab.com/gitlab-com/blog-posts/issues/278#note_17989540

*Gitlab & Jenkins*

> 1. Automating Jenkins jobs and managing it becomes a huge pain the larger you scale out in the number of Jenkins JOBs and Runners you manage.
2. Tools like Jenkinsfile help when the jobs and the git repos are 1:1.
3. People who scale out without jenkinsfile style, tend to arrive at a point where they're going to want the "Job-DSL" style from the blog post above.

- http://www.appliscale.io/blog/jenkins-job-dsl-automate-jenkins/


> I see Shell runners as the default place to START your CI journey, then graduate to VirtualBox runners, and then graduate to Dockerized CI if you need it, or if Dockerized CI is zero effort for you (standard docker containers maintained by someone else will work for you) and the slowness of spinning up containers doesn't bother you, or the benefits of guaranteed hygiene outweigh the downsides.
- @warren.postma

*From Jenkins to Gitlab : How one Developer Levelled up his CI Game*
> I’d like to share the knowledge and motivation that I think you may need to move up from Jenkins as your primary CI tool to using Gitlab CI primarily, and the reasons why I think it’s a level up, and finally the reasons why I still have certain use cases where you might want to use jenkins for some things. 

> *Jenkins things I miss in Gitlab CI* I miss timed builds, parameterized builds, and a rich ecosystem of plugins including things that let me view arbitrary rich (html) output from things like unit tests.  These issues are already logged in the correct Gitlab project issue trackers.  I hope that I might even be able to contribute one of these things that are missing from Gitlab CI myself.   That's the beauty of open source. I can add anything I feel like adding. So can you.  

> *Things I don’t miss* I don't miss manual configuration. I don't miss the travails of finding the correct triggers. I don't miss having a separate server to manually upgrade. In general I think I save at least one full working day a month that I used to spend managing Jenkins.  Gitlab CI is truly "set it and forget it".  Sometimes runner jobs freeze and need to be killed.  Gitlab CI is sensibly configured and generally gives me no trouble.   It is correct and does exactly what I want 90% of the time. It just works.   If I had moved to Jenkinsfiles and away from classic Jenkins freestyle projects, I would have only gotten about 50% of the benefits of moving to Gitlab CI

> *Conclusion* Gitlab CI is powerful because you get a lot of output and effect, from a small amount of input. It's a force-multiplier, a power tool.     Moving from the pre-CI world to Jenkins is like moving from manual tool world to a world with powertools.  Moving from Jenkins to Gitlab CI is like moving to a world where your needs are anticipated, and a swarm of helpful robots is working to do your bidding.  If you're currently using Jenkins, I hope you'll consider moving to a simple single-product solution, with Gitlab CE or EE as the basis of your source control and CI infrastructure.

Other points:

* Gitlab make inferrences which take out some of the work
* Jenkins needs to be setup to poll git hosting system
* Jenkins adopting a Jenkinsfile seems to indicate the `something-ci.yml` is the right way
* Gitlab knows about new projects, you don’t need to do setup
* Gitlab remembers if revisions/branches passed or failed
* It seems Jenkins has powers (html for unit tests, parametized builds) that developers would setup locally
* Jenkins is maintenance heavy
* *Gitlab opinion is correct 90% of the time*

https://docs.google.com/document/d/1_lwB72eBeoLl5Y_WYGZv-4L0AC22Nrf0zMx1WSitnb4/edit


*Also, if we have to - gitlab-ci and Jenkins work together* 
- https://medium.com/@teeks99/continuous-integration-with-jenkins-and-gitlab-fa770c62e88a#.ckzx9ukwa

* Scaling is automatic
* Easier for developers " build job is stored in an external admin-restricted tool. You need the right credentials to edit the build configuration, and it’s not obvious how to do it." Infact that barrier caused our team to setup Jenkins
- https://blog.trainline.eu/12703-building-on-gitlab-ci