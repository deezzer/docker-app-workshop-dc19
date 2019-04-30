# Exercise 4 - Configure your Application with Parameters

> **Time**: Approximately 15 minutes
>
> **Difficulty**: Easy

## Table of Contents



## Exercise Objectives

By the end of this exercise, you will have:

- Learned about Docker App parameters
- Added parameters to the voting app application
- Learned how to deploy an update to an already-deployed application



## Docker App Parameters

In addition to using environment variables, Docker App allows you to define parameters for an application that can be overridden at deploy-time. Examples of parameters might include port mappings, label definitions, or network names. _Almost_ everything in the Docker App compose file can be parameterized, except for the image names.


## Using a Parameter to Change Voting Options

By default, the vote and results services let you vote between Dogs and Cats. However, wouldn't it be neat if we could change that? Conveniently, both the `vote` and `results` services allow the definition of two environment variables (`OPTION_A` and `OPTION_B`) to let us change the voting choices. This sounds like a great opportunity for parameters!

1. In both the `vote` and `results` services, add environment variables named `OPTION_A` and `OPTION_B` with a value of `${optionA}` and `${optionB}`. The values are simply placeholders for the parameters.

    <details>
      <summary>Solution</summary>
    
    ```yaml
    services:
      vote:
        environment:
          OPTION_A: ${optionA}
          OPTION_B: ${optionB}
      results:
        environment:
          OPTION_A: ${optionA}
          OPTION_B: ${optionB}
    ```
    </details>

2. Before setting the parameter, run `docker app inspect` to see what happens.

    <details>
      <summary>Full Output</summary>
    
    ```console
    $ docker app inspect
    inspect failed: Action "com.docker.app.inspect" failed: failed to load Compose file: invalid interpolation format for services.vote.environment.OPTION_A: "required variable optionA is missing a value". You may need to escape any $ with another $.
    ```
    </details>

    What happened? The command fails because "required variable optionA is missing a value." In other words, **all parameters are required to have default values.** Those default values are specified in the `parameters.yml` file.

3. Specify the default values for `optionA` and `optionB` in the `parameters.yml` file. Let's set `optionA` to "Cats" and `optionB` to "Dogs". Remember that this file is a YAML file.

    <details>
      <summary>Solution</summary>
    
    ```yaml
    optionA: Cats
    optionB: Dogs
    ```
    </details>

4. Now, re-run the `docker app inspect` command to look at the output. You should see the parameters with their default values in the output now!

    <details>
      <summary>Full output</summary>
    
    ```console
    $ docker app inspect
    voting-app 0.1.0

    Maintained by: root

    Services (5) Replicas Ports Image
    ------------ -------- ----- -----
    db           1              postgres:9.4
    worker       1              dockersamples/examplevotingapp_worker
    result       1        5001  mikesir87/examplevotingapp_result
    vote         2        5000  mikesir87/examplevotingapp_vote
    redis        1              redis:alpine

    Networks (2)
    ------------
    backend
    frontend

    Volume (1)
    ----------
    db-data

    Parameters (2) Value
    -------------- -----
    optionA        Cats
    optionB        Dogs
    ```
    </details>

5. Now, try running the same `docker app inspect`, but add the `-s` flag to set optionA to "Moby" and optionB to "Molly". You should see the parameter values now have the replaced values.

    <details>
      <summary>Full output</summary>
    
    ```console
    $ docker app inspect -s optionA=Moby -s optionB=Molly
    voting-app 0.1.0

    Maintained by: root

    Services (5) Replicas Ports Image
    ------------ -------- ----- -----
    db           1              postgres:9.4
    worker       1              dockersamples/examplevotingapp_worker
    result       1        5001  mikesir87/examplevotingapp_result
    vote         2        5000  mikesir87/examplevotingapp_vote
    redis        1              redis:alpine

    Networks (2)
    ------------
    backend
    frontend

    Volume (1)
    ----------
    db-data

    Parameters (2) Value
    -------------- -----
    optionA        Moby
    optionB        Molly
    ```
    </details>


## Deploying an Updated Application

At this point, we have a deployed application. But, we want to
