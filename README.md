# Bert Extractive Summarizer

[![Build Status](https://travis-ci.com/dmmiller612/bert-extractive-summarizer.svg?branch=master)](https://travis-ci.com/github/dmmiller612/bert-extractive-summarizer)
[![license](https://img.shields.io/github/license/mashape/apistatus.svg?maxAge=2592000)](https://github.com/dmmiller612/bert-extractive-summarizer)
<img src="https://img.shields.io/pypi/v/bert-extractive-summarizer.svg" />

This repo is the generalization of the lecture-summarizer repo. This tool utilizes the HuggingFace Pytorch transformers library
to run extractive summarizations. This works by first embedding the sentences, then running a clustering algorithm, finding 
the sentences that are closest to the cluster's centroids. This library also uses coreference techniques, utilizing the 
https://github.com/huggingface/neuralcoref library to resolve words in summaries that need more context. The greedyness of 
the neuralcoref library can be tweaked in the CoreferenceHandler class.

Paper: https://arxiv.org/abs/1906.04165

## Rest of the original readme

This is fork of (dmmiller612/bert-extractive-summarizer)
[https://github.com/dmmiller612/bert-extractive-summarizer], the original readme can be read in the main repo.

## Aim of this fork

I am not a Machine Learning (ML) enthusiast yet :), so as the original repo had a docker file I wanted to do a quick deploy it on a service and get some text summaries working. For that I have chosen [Fly.io](https://fly.io) which deploys apps closer to the user so that it responds much faster.

## Running this summarizer API on Fly.io

Fly.io has great [docs](https://fly.io/docs/) so please have a look. You can run this text summarizer on fly.io with the following steps:

1. [Install](https://fly.io/docs/getting-started/installing-flyctl/) the flyctl CLI command
1. Register on fly with `flyctl auth signup` , if you already have a fly account login with `flyctl auth login`
1. Clone this repo with `git@github.com:geshan/bert-extractive-summarizer.git`
1. Then run `cd bert-extractive-summarizer`
1. Now the fun begins, execute `flyctl init`
1. Then type in a name for example: `text-summarizer`
1. Subsequently select the org, generally it will be your firstname-lastname
1. After that, select `Dockerfile` as the builder
1. It should create a fly.toml file at the project root now. Here is a screenshot of my output:
    ![Flyctl init output for bert-executive-summarizer](imgs/01fly-init.png?raw=true)
1. Lets get a bit more into it, run `flyctl deploy --dockerfile Dockerfile.service` -- probably time to make a coffee now as pulling the docker [image](https://hub.docker.com/r/geshan/bert-extractive-summarizer) (which is 3.5 GB), building it a bit more and pushing it to fly container registry then deploying it is going to take some time. Here is a screenshot of the deploy output:
    ![Flyctl deploy output for bert-executive-summarizer](imgs/02fly-deploy.png?raw=true)
1. 1 instance is unhealthy, and that is expected as of now. It is happening because of low resources (512 MB memory).
1. Then you can try `flyctl info` to see the info, and try `flyctl status` to see if it is running. My experience was the default 512 MB memory was not enough.
    ![Flyctl info and status output for bert-executive-summarizer](imgs/03fly-info.png?raw=true)
1. This is where it gets more interesing, now the resources have to be beefed up to make it run. Lets see what is allocated by default, to do it run `flyctl scale show` and what VM options are available with `flyctl platform vm-sizes`
    ![Flyctl scale and vm sizes output for bert-executive-summarizer](imgs/04fly-scale-show.png?raw=true)
1. To use a more powerful VM with 2 GB memory and 2 CPU run this command `flyctl scale vm cpu2mem2`, at least a 2 GB memory was required from my experience
    ![Flyctl scale vm output for bert-executive-summarizer](imgs/05fly-scale-vm.png?raw=true)
1. After like 1 minute try `flyctl status` to see if it is running with a 2 GB memory instance, something like below should be visible:
    ![Flyctl status output for bert-executive-summarizer](imgs/06fly-status.png?raw=true)
1. Now lets do `flyctl open` to see if it is running. The browser shows `Hello World!` now which is a good sign.
1. To try out a summarization run the following Curl:
    ````
    curl -X POST -H "Content-Type: text/plain" \
--data "Fly is a platform for applications that need to run globally. \
It runs your code close to users and scales compute in cities where your app is busiest. Write your code, package \
it into a Docker image, deploy it to Fly's platform and let that do all the work to keep your app snappy. You can go \
hands-on for free and launch a container on Fly or read on. What's the Idea Behind Fly? Your need to reduce latency \
to deliver your applications as fast as physics is our business. The old solution of globally duplicating resources \
in every datacenter near where customers are isn't sustainable. Being location smart, agile over time and clever with \
the cloud allows us to build a deployment platform that lets you reap the benefits of lower latency for your users \
wherever they are. What Fly Does Modern applications get packaged into containers and can carry with them all the \
code needed for them to operate. But, more often than not, they are deployed into specific datacenters, never to \
be moved. This is nothing like a container in the real world. Real containers are built to be transported where \
there's demand for them. With Fly.io, we're more like those real containers, moving around the world to where the \
demand is. Location-Smart Think of a world where your application appears in the location where it is needed. \
When a user connects to a Fly application, the system determines the nearest location for the lowest latency \
and starts the application there. And with automatic scaling, as more connections appear, the application is \
right sized for demand by creating new instances at that location. Agile over Time One of the best indications \
of demand is time, specifically the time of day - demand increases during the day when people work, and diminishes \
overnight. With Fly, our default is to use that information and let your applications follow the sun, relocating \
to where the clock shows the day beginning and being ready for the day's demand. If your users behave differently, \
you can change this behavior and set your own schedules. Clever with the Cloud Fly isn't just the simplest way to \
create and scale instances in datacenters around the globe. It's also the smartest way to do it. When your users \
connect to a Fly application, we analyze other nearby locations. If it's quicker to route their request transparently \
to a nearby instance that's already running, we do just that. Data travels between Fly datacenter locations over \
an encrypted backhaul running on fast connections. How Fly Works For… For Developers You can run most \
applications with a Dockerfile using the flyctl command. The first time you deploy an app, we assign it a \
global IP address. By default, apps listen for HTTP and HTTPS connections, though they can be configured \
to accept any kind of TCP traffic. When users connect to your global IPs, we dynamically assign compute \
resources in datacenters closest to them. More users might create demand for more resources in multiple \
locations worldwide, while low-traffic applications may only require a small amount of resources in a \
single location. For Operations Fly makes application deployment simple. Global availability and global \
IP addresses put your applications anywhere with a single command. Automatically generated TLS certficates \
help secure your applications. And once your application is deployed, Fly keeps your performance up by \
sending users on the shortest, fastest path to where your application is running. Why Fly? Compare those \
Fly features with a traditional non-Fly global cloud. There you create instances of your application at \
every location where you want low latency. Then, as demand grows, you'll end up scaling-up each location \
because scaling down is tricky and would involve a lot of automation. The likelihood is that you'll end \
up paying 24/7 for that scaled-up cloud version. Repeat that at every location and it’s a lot to pay for. \
Why are we Making Fly? Despite the benefits of location-smart, time-agile and cloud-clever applications, \
there’s been no good platform for building applications that work like this. This is what Fly has set out \
to fix. In the process we want to make application distribution platforms as ubiquitous as CDNs." \
https://text-summarizer.fly.dev/summarize\?ratio\=0.1
    ````
1. You should get an output like below:
    ````
    {"summary":"Fly is a platform for applications that need to run globally. Being location smart, agile over time and clever with the cloud allows us to build a deployment platform that lets you reap the benefits of lower latency for your users wherever they are. This is nothing like a container in the real world. There you create instances of your application at every location where you want low latency. Then, as demand grows, you'll end up scaling-up each location because scaling down is tricky and would involve a lot of automation."}
    ````
1. So we just summarized the fly.io main doc page to 10% (0.1 ratio) with the BERT based summarizer.
1. Hope you liked it!
