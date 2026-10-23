# Design Rationale

## Multi-stage Docker build

The multi-stage Dockerfile separates the build environment from the runtime environment. The first stage uses Node.js to install dependencies and build the website. The second stage uses NGINX to serve only the generated website files. This avoids including the Node.js build toolchain and development dependencies in the final runtime image. Docker documents this approach as a way to selectively copy build artifacts into a separate final stage [1].

During testing, the single-stage comparison image was reported as 1.65 GB, while the multi-stage image was 93.6 MB. These are the sizes reported by the local Docker image listing. The multi-stage image was substantially smaller, reducing the amount of image data to store and transfer. Image sizes may change when base images or dependencies are updated.

## Service separation and startup health

The Compose configuration separates the static website (web) from Redis (cache). NGINX serves the website on host port 8080, while Redis provides a separate cache service on its default container port. The Redis health check runs redis-cli ping, and Compose waits for the cache to become healthy before starting the web service. Docker documents using health checks and depends_on conditions to control service startup order [2].

The static website does not currently read from or write to Redis. This project demonstrates service separation and startup ordering, not a fully integrated caching application. During testing, the website continued returning HTTP 200 after Redis was stopped. This is expected because the web service does not depend on Redis at runtime.

## Local serverless execution

The AWS SAM scaffold provides a separate Hello World function. The function was invoked locally using sam local invoke HelloWorldFunction and returned status code 200 with the message hello world. AWS documents sam local invoke as a way to invoke a function locally for testing without deploying it to AWS [3]. This allows the function to be tested without creating cloud resources.

## References

[1] Docker Docs, "Multi-stage builds."
https://docs.docker.com/build/building/multi-stage/

[2] Docker Docs, "Control startup and shutdown order in Compose."
https://docs.docker.com/compose/how-tos/startup-order/

[3] AWS, "Testing and debugging serverless applications."
https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/using-sam-cli-local-invoke.html
