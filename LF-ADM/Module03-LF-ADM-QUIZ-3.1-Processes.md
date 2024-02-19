# Quiz 3.1: Configure Resource Limits
1. Configure resource limits for local group instructor, so that a maximum of 15 processes can be start.

2. This process limit is to be configured with hard and soft limit equivalency.

3. Configure resource limits for local user instructor, so that this user is not affected by the above process limitation, but only by the systems defaults for non-root users, i,e. So that an unlimited number of processes can be started.

4. This process limit exclusion is to be configured with hard and soft limit equivalency

# Answer
```lua
nano /etc/security/limits.conf
```
```lua
@instructor        soft    nproc           15
@instructor        hard    nproc           15
```
