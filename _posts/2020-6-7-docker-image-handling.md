---
layout: post
title: Should we use same docker image for development / QA / production environment?
excerpt: Briefly describes how should one handle docker images to avoid last-minute surprises in production environment.
---

I need to understand what is going on with my application. I have got good test coverage, adopting modern dev-ops practices of CI and CD, test automation, agile …. but every-time I click that trigger for production deployment, the application finds some way of failing me.

Sounds like a problem of past — I can learn a lot from you.

Sounds familiar ? I can share some of my learnings.

## Scenario
It’s one of the founding principles of continuous delivery that all your tests need to run in a “production-like” environment in order to have any assurance of a stable release. The more production-like your DEV and QA environments are, the more confident you can be.

Following this principle, we should have a single docker image and it should be promoted across environment from DEV => QA => PROD

But I need to differentiate between DEV, QA and PROD images for following reason:

1. _Access control_: Limited people should have access to PROD docker images for preventing accidental deletion.

2. _Identifying candidate for promotion to QA/PROD_: Not all images could be promoted to PROD. Only the ones that are tested in QA can be promoted to PROD. Similarly not all images can be promoted to QA. Images generated from feature branches remain in DEV. Only the ones that are generated from release / master branch are promoted from DEV to QA.

## Typical solution pattern

We can use same docker image across environment but tag it with environment name.

One can create basic react-app docker image and tag it as dev for DEV environment. Similarly re-tagging dev docker image as qa and prod for QA and PROD environment respectively.

Feature branches can be tagged differently and hence prevented from promotion to QA/PROD. Only images that are tagged as dev can be promoted to QA. Similarly only images that are tagged as qa can be promoted to PROD.

## Implementation

### Step 1:
Let’s create basic react app

### Step 2:
Let’s dockerize it, give it a dev tag and run it on dev machine using following commands

{% highlight bash %}
docker build -t charu123/react-app:dev .
docker push charu123/react-app:dev
docker run charu123/react-app:dev
{% endhighlight %}

### Step 3:
Let’s promote dev tag to QA environment by retagging it with qa and running it on QA machine.

{% highlight bash %}
docker pull charu123/react-app:dev
docker image tag charu123/react-app:dev charu123/react-app:qa
docker push charu123/react-app:qa
docker run charu123/react-app:qa
{% endhighlight %}

Similarly we can repeat step 3 for promoting QA docker image to PROD environment.

## Conclusion
Above approach helps us in solving two identified problems
Access control: Since PROD image are tagged differently, we can prevent them from accidental deletion

Identifying candidate for promotion to QA/PROD: Since docker images for feature branches, release / master branch are tagged differently, one will only promote images from release / master branch to QA. And one will only promote QA tagged images to PROD environment.

One can enhance feat, dev, qa, prod tag to support multiple versions as well.

Sometimes I also feel need of having different configuration per environment and face challenges with single docker images. But so far, getting environment specific config on app launch works for me.

Soon will post an article on same.

Till then, Happy Coding :)