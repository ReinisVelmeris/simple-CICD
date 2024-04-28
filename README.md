# simple-CICD

Runs on self-hosted windows runner

Problem:
after the deployment job execution the actions-runner seems to be killing the orphan processes
which is why the tests are not currently working -the started service has stoped working