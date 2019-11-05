---
layout: post
title: Automate Everything
subtitle: Including the minutia
# bigimg: /img/path.jpg
tags: [cli, tools, automation, devops]
---

What is DevOps?

> DevOps is a set of practices that automates the processes between software development and IT teams, in order that they can build, test, and release software faster and more reliably. The concept of DevOps is founded on building a culture of collaboration between teams that historically functioned in relative siloes.

What is CI/CD?

> Continuous Integration (CI) is a development practice that requires developers to integrate code into a shared repository several times a day. Each check-in is then verified by an automated build, allowing teams to detect problems early. By integrating regularly, you can detect errors quickly, and locate them more easily.

At the heart of every engineering firm, the concepts of atomation are being spued from the mouths of everyone within the organiztion. From leadership and the non-technical, all the down to the systems analyst and junior engineers. "Automate, automate, automate" they say, "faster time to market", "feature flags are a must", and of course, "we should deploy to production every day".  

Let me preface the rest of this post with the fact that I do *NOT* disagree with any of the aformentione quotes above. I have seen a tremendous increase in over-arching efficiencies within the DevOps space over the last decade. We saw it with better source control tools such as git and GitHub, and opensource delivery tools like Jenkins, allowing us to create automated deployments whether your team was using `GitFlow` or [trunk-based development](https://trunkbaseddevelopment.com/5-min-overview/).  

Let's not disinclude our friends docker and kubernetes, paving the way for next-level automation. First was `docker`, which gave us OS-level virtualization to deliver software in packages called containers. Then came `kubernetes`, a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation. (We first had to learn how to pronounce it!)
Throw in helm charts, which gave us a way to add a templating engine around the deployment of docker images to kubernetes, some would say we have reached engineering utopia. My work is done here...

... or is it?

Now that we have automated the process of delivering our software, including but not limited to the testing of said software (acceptance, performance, and e2e), the static code analysis, binary storage of packages, and deployment of components to production, it's time we look elsewhere to find opportunities in effeiciency.  

## The minutia

I recently spent time as a `boots on the ground` engineer for one of the product teams I oversee. It had been a hot minute since the last time I had done day-to-day scrumming with agile team, but it did not take me long to see that there were opportunities to automate even the simplist of everyday tasks within software delivery. The team was using various communication channels such as Slack/Flowdock for chat, GH Pages and Confluence wiki for documentation. However, there were several times I saw questions like "How did you set up a token to authenticate to that", or "can someone send me the curl command used to post a document there", etc. After some quick math, it became abunduntly clear that there was ample time spent repeating mundane tasks over and over everyday to accomplish simple and small tasks to complete user stories... the minutia.  

I could go into detail how something as simple as creating gists in GitHub to share would suffice, and I use this technique quite frequently. As a matter of fact, an opensource tool [Lepton](https://hackjutsu.com/Lepton/) has become one of my favorite tools for managing all of my gists! I could also go on and and regarding tools like [Inomnia](https://insomnia.rest/) as a REST client have simplied development workflows numerous times for my teams. But today, I want to talk about using acustom built, project specific `cli` might be one of the most under valued tool in the shed.  

## The cli

I have been promoting the concept of local sandbox environments for all of my engineering teams. The ability to quickly stand up production like replica environments have become increasingly easy with the advent of tools like kubernetes and helm. However, getting them initialized the first time, with all needed sofware components within a system running, and initiating an end-to-end test can be cumbersome at times, especially when new engineers are introduced to the team. The documentation to on-board an egineer alone is sometimes pages long.

For this post, I will speak to a particular project that leveraged SPARK on Kubernetes to perform traditional ETL jobs. Each client, has a specific set of `instructions` to transform their data to a common format used in downstream systems. Spark would connect to S3 buckets, consume delimited text files, leverage the `instructions` to perform typical data tranformations on DataFrames, write transformed delimited files back to S3 buckets for consumption downstream.

Peforming this e2e without automation wouldn't be enjoyable in the least bit, ignoring the fact that it would take a tremendous amount of time putting all of the pieces in place to execute correctly. This is where the `cli` comes in! We decided to take one two-week sprint to engineer a cli that would automate 2 critical funtions for this team:

1. Initialize Local Envrionment
2. Execute E2E Test

### Init

The intialize local command (`init`) performed the following steps:  

1. Cloned all system components to temporary directory
2. Executed helm charts for all system components
3. Initialized local kubernetes environment
4. Cleaned temporary directory

Our set-up instructions went from a myriad of prerequisites and 3 pages of `download this`, and `run this` to only 5 prerequisites (SPARK, Docker for Mac, git, SBT, Node), with SBT and SPARK the only two that aren't required for all projects. The 3 pages of instructions to onboard a new engineer went to:  

```sh
npm i @company/cli
cli init
```

### E2E

Running the `cli e2e` command from the terminal was even more magical. 🎉  

![alt text][e2e]

The `e2e` command was responsible for uploading test files to [minio](https://min.io/) an Amazon S3 Compatible Object Storage solution. Upon completion, it would then make http call to receive client specific `instructions` from a SpringBoot api in local kubernetes. It would pass the instructions as a parameter to the spark-submit call. This would create a [SPARK](https://spark.apache.org/docs/latest/running-on-kubernetes.html) driver pod in kubernetes that would execute the instructions provided, and place outputted files in a separate bucket in minio. After completion of the spark-submit, the cli would retrieve the newly placed files, and do data-driven assertions that the transformations met the expected output. (You even get a 🍕🍺 if all is well)

This e2e process ran in 1-2 minutes each time, and could be executed from soup-to-nuts in less than 5 minutes. In a world where engineers move into, and out of project teams constantly, I feel confident that I can stand behind my original statement that there is a ton of opportunity to create efficiencies in our workstreams, and I think a custom `cli` is a great start!

[e2e]: https://github.com/seesharpguy/seesharpguy.github.io/blob/master/img/cli.png "cli end-to-end"
