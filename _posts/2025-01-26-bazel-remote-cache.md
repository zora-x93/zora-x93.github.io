---
layout: post
title: Study notes of Bazel Remote Cache
tags: en tech bazel ci notes
---
Recently I'm busy with learning a new tool called **Bazel**, which is for software build and test automation. I think it might be good to summarize the findings and write down some notes here.

As I'm still learning, this post includes just **very basic concepts** which might help Bazel beginners to understand where to start when introducing CI for Bazel, and I'm not aiming to explain every details here.

# Bazel build
Bazel has a key concept `action`. Bazel breaks each build into discrete steps, which are called **actions**. You can see them as steps during a build, and each of them has inputs, output names, a command line and environment variables defined.

The outputs of actions can be set as **cache**, which Bazel will reuse to achieve incremental builds. The outputs are in form of a list of output file names and their hashes.

So far, the Bazel commands I saw are pretty simple: `bazel build` executes the build while `bazel test` not only runs tests but also builds missing targets. Using a flag `--output_base` followed by a directory path will tell Bazel a certain location to store cache, which is an important config in CI.

# Remote Cache
To speed up the build of a large Bazel project for CI, **remote cache** is for sure needed as you probably won't want each CI loop to build from scratch--though a clean build for individual modules in a poly-repo structure is usually the best practice, it's not true for a large, heavy mono-repo. Actually, one reason I'm looking into Bazel is that it's well designed to support incremental build. 

And because CI tasks are usually performed on a build farm, i.e. a bunch of machines, the best practice is to utilize Bazel remote cache to share the cache among those build machines instead of using disk cache that stored locally.

Bazel remote cache consists of 
- action cache, a map of action hashes to action result metadata
- a content-addressable storage(CAS) of output files

*What is CAS?*
It's a way of storage, in contrast to location-addressable storage. The latter one is more common, as the file structure on our hard drives or cloud storage like Google Drive are all following location-addressable method, keeping track files based on their file paths. CAS, however, generates a unique hash code for each file or object. The advantage of CAS is that when files saved multiple times the system will recognize the identical files and save them only once. A typical example where CAS is used is **Git**.

## How Bazel build uses remote caching?
There are four methods for Bazel to use remote caching:
1. Read and write to the remote cache
2. Read and/or write to the remote cache expect for specific targets
3. Only read
4. Not using it

### Read and Write steps (in method 1)
1. Bazel creates a graph of targets that need to be built, then creates a list of required actions.
2. Bazel checks locally for existing build outputs and resues them.
3. Bazel checks the **cache** for existing build outputs. A **cache hit** happens if Bazel finds and retrieves an output.
4. Bazel executes actions where the outputs were not found.
5. New build outputs are uploaded to remote cache.

# Setup a server
The key factors to consider to maximize performance:
- Networking speed
- Security
- Ease of management

There are many options on how to setup the remote caching:
- nginx -- remote cache as a web server
- bazel-remote
  - open source remote build cache, provided by 3rd party
  - available as a docker image
  - https://github.com/buchgr/bazel-remote/
- Google Cloud Storage
- Apache httpd
- AWS S3
- etc.

It's also possible to enable authentication for remote cache with user and password.

# Run Bazel with Remote Cache
When the correct flags are added to your Bazel command, or `.bashrc` includes the flags, a Bazel build will start to use the remote cache.

Of course, if authentication is needed, Bazel has to be configured accordingly.

`bazel build --remote_cache=http://your.host:port` sets the remote cache.

`bazel build --remote_upload_local_results=false` stops local cache from being written to remote, i.e. using remote cache as read-only.
