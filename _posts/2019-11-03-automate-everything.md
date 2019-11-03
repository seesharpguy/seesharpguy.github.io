---
layout: post
title: Automate Everything
subtitle: including the minutia
# bigimg: /img/path.jpg
tags: [cli, tools, automation, devops]
---

What is DevOps?

> DevOps is a set of practices that automates the processes between software development and IT teams, in order that they can build, test, and release software faster and more reliably. The concept of DevOps is founded on building a culture of collaboration between teams that historically functioned in relative siloes.

What is CI/CD?

> Continuous Integration (CI) is a development practice that requires developers to integrate code into a shared repository several times a day. Each check-in is then verified by an automated build, allowing teams to detect problems early. By integrating regularly, you can detect errors quickly, and locate them more easily.

At the heart of every engineering firm, the concepts of atomation are being spued from the mouths of everyone within the organiztion. From leadership and the non-technical, all the down to the systems analyst and junior engineers. "Automate, automate, automate" they say, "faster time to market", "feature flags are a must", and of course, "we should deploy to production every day".

Let me preface the rest of this post with the fact that I do NOT disagree with any of the aformentione quotes above. I have seen a tremendous increase in over-arching efficiencies within the DevOps space over the last decade. We saw it with better source control tools such as git and GitHub, and opensource delivery tools like Jenkins, allowing us to create automated deployments whether your team was using `GitFlow` or [trunk-based development](https://trunkbaseddevelopment.com/5-min-overview/). Let's not disinclude our friends docker and kubernetes, paving the way for next-level automation. First was docker, which gave us OS-level virtualization to deliver software in packages called containers. Then came `kubernetes`, a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation. (We first had to learn how to pronounce it)
Throw in helm charts, which gave us a way to add a templating engine around the deployment of docker images to kubernetes, some would say we have reached engineering utopia. My work is done here...

... or is it?