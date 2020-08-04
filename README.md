# Bert Extractive Summarizer on Fly.io

I am not a Machine Learning (ML) enthusiast yet, but I was digging into the subject when I discovered the [Bert executive summarizer](https://github.com/dmmiller612/bert-extractive-summarizer) which is an open source summarizer. I wanted to do a quick deploy with it onto a service and start experimenting with text summarization. For the service I have chosen Fly.io which deploys apps closer to the user so that it responds much faster.

Let's look at the summarizer we will be deploying in more detail.

## Running the summarizer API on Fly.io

Fly.io has great [docs](https://fly.io/docs/) so please have a look. You can run this text summarizer on fly.io following the steps below:

### Prerequisites

1. [Install](https://fly.io/docs/getting-started/installing-flyctl/) the flyctl CLI command
1. Register on fly with `flyctl auth signup` , if you already have a fly account login with `flyctl auth login`

### Steps

1. Clone this repo with `git@github.com:geshan/bert-extractive-summarizer.git` if you are logged in with SSH support enabled. Otherwise try `git clone https://github.com/geshan/bert-extractive-summarizer.git`.
1. Then run `cd bert-extractive-summarizer`
1. Now the fun begins, execute `flyctl init `
1. Then type in a name for example: `text-summarizer`, if it is taken try a different name re-running `flyctl init`.
1. Subsequently select the org, generally it will be your firstname-lastname
1. After that, select `Dockerfile` as the builder
1. Lets get a bit more into it, run `flyctl deploy --dockerfile Dockerfile.service` -- probably time to make a coffee now as pulling the docker [image](https://hub.docker.com/r/geshan/bert-extractive-summarizer) (which is 3.5 GB), building it a bit more and pushing it to fly container registry then deploying it is going to take some time. Here is a screenshot of the deploy output:
1. 1 instance is unhealthy, and that is expected as of now. It is happening because of low resources (512 MB memory).
1. Then you can try `flyctl info` to see the info, and try `flyctl status` to see if it is running. My experience was the default 512 MB memory was not enough.
1. This is where it gets more interesing, now the resources have to be beefed up to make it run. Lets see what is allocated by default, to do it run `flyctl scale show` and what VM options are available with `flyctl platform vm-sizes`
1. To use a more powerful VM with 2 GB memory and 2 CPU run this command `flyctl scale vm cpu2mem2`, at least a 2 GB memory was required from my experience
1. After like 1 minute try `flyctl status` to see if it is running with a 2 GB memory instance, something like below should be visible:
1. Now lets do `flyctl open` to see if it is running. The browser shows `Hello World!` now which is a good sign.
1. To try out a summarization run the following Curl, replace the URL with your service's URL:
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
1. So we just summarized the fly.io main doc page content to 10% (0.1 ratio) with the BERT based summarizer.
1. Hope you liked it!

## Possible applications

The possibilities for this text summarizer are endless, you could potentlially build a news summarizer app that takes all the COVID-19 news and gives out a concise digest to your readers. Everyone doesn't like reading long texts so summarizing it to 10% or 20% will save a lot of time for the reader without missing important information.

Of course the summaries won't be perferct, thats where you can read more about the BERT algorithm and read the configs on the [main repo](https://github.com/dmmiller612/bert-extractive-summarizer). Happy MLing :)
