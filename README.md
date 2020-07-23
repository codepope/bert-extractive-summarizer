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
    
1. Lets get a bit more into it, run `flyctl deploy` -- probably time to make a coffee now as pulling the docker [image](https://hub.docker.com/r/geshan/bert-extractive-summarizer) (which is 3.5 GB), building it a bit more and pushing it to fly container registry then deploying it is going to take some time.
1. Then you can try `flyctl info` to see the info, and try `flyctl status` to see if it is running. My experience was the default 512 MB memory was not enough.
1. Now the resources had to be beefed up to make it run. Lets see what is allocated by default, to do it run `flyctl scale show` 
1. To use a more powerful VM with 2 GB memory and 2 CPU run this command `flyctl scale vm cpu2mem2`, at least a 2 GB memory was required from my experience
1. 
