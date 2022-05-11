# satellite-study
Satellite Nanoservice Design

![icon](https://cdn.eyzi.dev/satellite-study/icon.png)

###### What's smaller than a microservice?

## Overview

I'm experimenting on a concept of a service or microservice (or, as I
prefer to call it, a nanoservice) whose sole purpose is to aid another
service or microservice on even granular functionalities. You can think
of this as the `utils` equivalent to a codebase, or a plugin to a
software.

I think of it much like a satellite to a planet. An non-essential,
easily-detachable module but adds functionality.

I emphasize on the property that its *sole* purpose is to be used by
another service or microservice. A satellite nanoservice should not
function by itself and should not have any effect on any
system when it is not attached to any service.

## Definitions and Rules

- APP refers to a particular service or microservice
- SATELLITE APP refers to the satellite nanoservice
- ORBIT refers to the APP port or process where the SATELLITE attaches
- NORMAL FLOW refers to the flow of operation when the SATELLITE APP is
not attached

1. The APP must function with or without a SATELLITE APP
2. The SATELLITE APP must be able to dynamically attach to and detach
from the APP
3. The APP should disregard any ORBIT errors and should be logged and
handled by the SATELLITE APP instead
4. The ORBIT must be a minor supporting feature of the APP. It should
not take a significant time to implement or maintain.
5. The SATELLITE APP must only attach to the APP through the ORBIT
6. The SATELLITE APP must not have any functionality or side effects
when it is not attached to an APP
7. Multiple APPs may use the same instance SATELLITE APP
8. An ORBIT should only allow a single SATELLITE APP to attach
9. Taking down a SATELLITE APP at any point during operation should not
have any effect other than undoing the specific functionality that it
offers.
10. When the availability and/or reliability of a SATELLITE APP
becomes crucial to the operation, it must be converted into a
full-fledged, independent APP.

## Uses

This can be useful for mocking, circuit breaking, external logging, or
testing as it keeps the application clean and focused on delivering
their business needs.

When a smaller, non-essential part of the APP is expected to changes at
a more frequent rate than the regular deployment of the APP, this
design can be useful.

This keeps the APP from being redeployed or taken down for an update
that only affects a minor part of it. For instance, a config change to
mitigate an issue.

## Design

### Idea #1: API method

An APP's ORBIT can have a polling system to regularly check whether a
SATELLITE APP is available to attach to it. Each ORBIT, then, should
not have a universal implementation but catered specifically to the
type of SATELLITE APP that can attach to it.

When an ORBIT is free, it redirects the APP's request to NORMAL FLOW,
and when a SATELLITE APP is attached to it, it attempts to forward the
request to the SATELLITE APP and returns its response to the APP.

In the instance of the SATELLITE APP crashing or returning an error, or
the ORBIT producing an error while attempting to forward to request to
the SATELLITE APP, the ORBIT must not fail and redirect the APP's
request to NORMAL FLOW.

Ideally, the SATELLITE APP should have a fast process to which the
ORBIT can have a small timeout when it doesn't receive any response
back. In that case, the ORBIT must consider it a failure and redirect
the APP's request to NORMAL FLOW.

##### 🚧 under construction
