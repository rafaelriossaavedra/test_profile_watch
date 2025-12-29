# Description

This is a small test repo for testing compose profiles and watch mode.
 
## Setup

There is a compose in the main directory, that start common services.
Then there is a subdirectory (`app1', 'app2') for each of the "applications". Each of this applications have its own compose file that start its own services.

All the services have:

- A directory to monitor file changes. This is montinored by the `watch` directive and only takes effect if `watch` parameter is added to `docker compose` command.
- A profile asigned. So, you can indicate which profile(s) one you want to start.

# Examples

## Starting

