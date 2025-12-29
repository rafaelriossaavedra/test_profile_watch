# Description

This is a small test repo for testing compose profiles and watch mode.
 
## Setup

There is a compose in the main directory, that start common services.
Then there is a subdirectory (`app1`, `app2`) for each of the "applications". Each of this applications have its own compose file that start its own services.

In each of the compose files, the services have:

- A directory to monitor file changes (`src_XXX`). This is montinore`watch` directive and only takes effect if `watch` parameter is added to `docker compose` command.
- A profile asigned. So, you can indicate which profile(s) one you want to start.

The way to indicate that a service is always started is by **not** assigning a profile.

You will find that I am using: `APP1_DIR=$PWD/app1 APP2_DIR=$PWD/app2` this is just to indicate where to find the "applications" directories. 

# Examples

## Profiles
### Starting common services

```bash
$ docker compose -f compose.yml -f app1/compose.yml -f app2/compose.yml up
[+] up 3/3
 ✔ Network test_profile_watch_default      Created                                                                  0.0s 
 ✔ Container test_profile_watch-common_B-1 Created                                                                  0.0s 
 ✔ Container test_profile_watch-common_A-1 Created                                                                  0.0s 
Attaching to common_A-1, common_B-1
```

Only services `common_A` and `common_B` defined in the root compose file are started as they don't have any profile asigned.

### Starting one service

In the example, there is only once service that belongs to profile `p4`, so let's start it.

There are a couple of ways of starting profiles, by passing them to a paremeter or by using an environment variable.

```shell
$ APP1_DIR=$PWD/app1 APP2_DIR=$PWD/app2 COMPOSE_PROFILES=p4 docker compose -f compose.yml -f app1/compose.yml -f app2/compose.yml up

or

$ APP1_DIR=$PWD/app1 APP2_DIR=$PWD/app2 docker compose -f compose.yml -f app1/compose.yml -f app2/compose.yml --profile p4 up
```

```shell
 ✔ Network test_profile_watch_default      Created                                                                  0.0s 
 ✔ Container test_profile_watch-common_B-1 Created                                                                  0.0s 
 ✔ Container test_profile_watch-common_A-1 Created                                                                  0.0s 
 ✔ Container test_profile_watch-svr_D-1    Created                                                                  0.0s 
Attaching to common_A-1, common_B-1, svr_D-1
```

### Starting several services from the same compose file, eg. profile `p1`

```shell
$ APP1_DIR=$PWD/app1 APP2_DIR=$PWD/app2 COMPOSE_PROFILES=p4 docker compose -f compose.yml -f app1/compose.yml -f app2/compose.yml up
```

### Starting several services from the same compose file, eg. profile `p1`

```shell
$ APP1_DIR=$PWD/app1 APP2_DIR=$PWD/app2 COMPOSE_PROFILES=p1 docker compose -f compose.yml -f app1/compose.yml -f app2/compose.yml up

[+] up 4/4
 ✔ Container test_profile_watch-common_B-1 Created                                                                  0.0s 
 ✔ Container test_profile_watch-svr_A-1    Created                                                                  0.0s 
 ✔ Container test_profile_watch-common_A-1 Created                                                                  0.0s 
 ✔ Container test_profile_watch-svr_F-1    Created                                                                  0.0s 
Attaching to common_A-1, common_B-1, svr_A-1, svr_F-1
```

### Starting several profiles form the different compose files, eg. profiles `p1` and `p4`

It doesn't matter where the profiles are located because profiles are shared accross files.

```shell
$ APP1_DIR=$PWD/app1 APP2_DIR=$PWD/app2 COMPOSE_PROFILES=p1,p4 docker compose -f compose.yml -f app1/compose.yml -f app2/compose.yml up

[+] up 6/6
 ✔ Network test_profile_watch_default      Created                                                                  0.0s 
 ✔ Container test_profile_watch-svr_D-1    Created                                                                  0.0s 
 ✔ Container test_profile_watch-common_B-1 Created                                                                  0.0s 
 ✔ Container test_profile_watch-svr_F-1    Created                                                                  0.0s 
 ✔ Container test_profile_watch-svr_A-1    Created                                                                  0.0s 
 ✔ Container test_profile_watch-common_A-1 Created                                                                  0.0s 
Attaching to common_A-1, common_B-1, svr_A-1, svr_D-1, svr_F-1
```

### Starting all profiles

```shell
$ APP1_DIR=$PWD/app1 APP2_DIR=$PWD/app2 COMPOSE_PROFILES="*" docker compose -f compose.yml -f app1/compose.yml -f app2/compose.yml up

[+] up 6/6
 ✔ Container test_profile_watch-svr_D-1 Created                                                                     0.0s 
 ✔ Container test_profile_watch-svr_B-1 Created                                                                     0.1s 
 ✔ Container test_profile_watch-svr_G-1 Created                                                                     0.1s 
 ✔ Container test_profile_watch-svr_C-1 Created                                                                     0.0s 
 ✔ Container test_profile_watch-svr_A-1 Created                                                                     0.0s 
 ✔ Container test_profile_watch-svr_F-1 Created                                                                     0.1s 
Attaching to common_A-1, common_B-1, svr_A-1, svr_B-1, svr_C-1, svr_D-1, svr_F-1, svr_G-1
```

## Watch mode

The watch mode in docker compose allow to react to file changes outside the container. There are several actions that can be produced when the file change is detected. In the examples here I only used `sync` but this in not important.

The watch documentation can be found [here](https://docs.docker.com/compose/how-tos/file-watch/).

### Examples

To start the watch mode , the `watch` parameter need to be passed to docker compose, and the in the services a `develop` section needs to be added. In this section you can include what to monitor and the action to be taken. Check the documentation for the details.

As the container are waiting infinitelly in these examples, it is not really important the action or the contents.
We only are interested in when the actions are triggered and what it is doing.

We are going to start profiles `p1` and `p4`

```shell
$ APP1_DIR=$PWD/app1 APP2_DIR=$PWD/app2 COMPOSE_PROFILES=p1,p4 docker compose -f compose.yml -f app1/compose.yml -f app2/compose.yml watch

[+] Building 0.1s (8/8) FINISHED                                                                                                                        
 => [internal] load local bake definitions                                                                                                         0.0s
 => => reading from stdin 552B                                                                                                                     0.0s
 => [internal] load build definition from Dockerfile                                                                                               0.0s
 => => transferring dockerfile: 67B                                                                                                                0.0s
 => [internal] load metadata for docker.io/library/debian:12                                                                                       0.0s
 => [internal] load .dockerignore                                                                                                                  0.0s
 => => transferring context: 2B                                                                                                                    0.0s
 => [1/2] FROM docker.io/library/debian:12                                                                                                         0.0s
 => CACHED [2/2] RUN mkdir /app                                                                                                                    0.0s
 => exporting to image                                                                                                                             0.0s
 => => exporting layers                                                                                                                            0.0s
 => => writing image sha256:02604215bf62a7c5e6ab4549816899e7303f0b2049d2ff11e0bfbcba410273c7                                                       0.0s
 => => naming to docker.io/library/test_profile_watch-svr_a                                                                                        0.0s
 => resolving provenance for metadata file                                                                                                         0.0s
[+] up 7/7
 ✔ Image test_profile_watch-svr_a          Built                                                                                                   0.1s 
 ✔ Network test_profile_watch_default      Created                                                                                                 0.0s 
 ✔ Container test_profile_watch-common_b-1 Created                                                                                                 0.0s 
 ✔ Container test_profile_watch-svr_d-1    Created                                                                                                 0.0s 
 ✔ Container test_profile_watch-common_a-1 Created                                                                                                 0.0s 
 ✔ Container test_profile_watch-svr_f-1    Created                                                                                                 0.0s 
 ✔ Container test_profile_watch-svr_a-1    Created                                                                                                 0.0s 
Watch enabled
```

As we can see, docker has `watch enabled`.

Now let's trigger some action:

```shell
$ touch app1/src1/file.txt
$ touch src_com1/file.txt
```

We can see in the compose output that it is triggering the desired action

```shell
Syncing service "svr_a" after 1 changes were detected
Syncing service "common_a" after 1 changes were detected
```

However, you can not indicate which services you want to use `watch` for:

```shell
$ APP1_DIR=$PWD/app1 APP2_DIR=$PWD/app2 COMPOSE_PROFILES=p1,p4 docker compose -f compose.yml -f app1/compose.yml -f app2/compose.yml watch svr_f

[+] up 2/2
 ✔ Network test_profile_watch_default   Created                                                                                                    0.0s 
 ✔ Container test_profile_watch-svr_f-1 Created                                                                                                    0.0s 
Watch enabled
```

in this output, only `svr_f` was started and not all the services under the profiles `p1,p4`.

# Conclusions for out use case

- The watch mode is all or nothing, so you can not specify the which ones do you want to watch
- To start different services you only need to set a profile for each of them and indicate them when starting. You can predefine some enviroment variables in your `.bash_profile` and use them for `COMPOSE_PROFILES` :

```shell
$ cat .bash_profile
...
MY_CONFIG_1=p1,p4
MY_CONFIG_2=p3,p2
...

$ COMPOSE_PROFILES=$MY_CONFIG_1 docker compose -f compose.yml -f app1/compose.yml -f app2/compose.yml watch 
```

or have an  `start_compose.sh` that handle it:


```shell
#!/bin/bash

# Choose which configuration you want to start
DESIRED_CONFIG=MY_CONFIG_1

# Add watch
WATCH_ENABLED=1

[ "$WATCH_ENABLED" = "1" ] && watch_command="watch" || watch_command=""

# Defined configurations
MY_CONFIG_1=p1,p4
MY_CONFIG_2=p3,p2
MY_CONFIG_3=p1,p2

# Find compose files
files=$(find . -mindepth 2 -maxdepth 2 -name 'compose.yml' -printf '-f %p ')
if [ -n "$files" ]; then
    files=${files% }  # Remove trailing space
else
    echo "No compose.yml files found"
fi

# Start compose
COMPOSE_PROFILES=${!DESIRED_CONFIG} docker compose $files "${watch_command}"
```

