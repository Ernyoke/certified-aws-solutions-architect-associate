# CICD - Continuous Integration / Continuous Delivery

## Continuous Integration

- Developers push code to a repository (CodeCommit)
- A testing/build server checks the code as soon as it is pushed (CodeBuild)
- Developers get feedback about the build status and tests
- CI helps:
    - Find bugs early and fix them
    - Deliver faster as the code is tested
    - Deploy often

## Continuous Delivery

- Ensure that software can be released reliably whenever needed
- Ensures deployment happens ofter and quickly
- We can shift away from "one release every 3 months" mindset to "5 releases a day" (lol)
- This happens with automated deployments (CodeDeploy)
