Dockerization & New Relic
We use a multi-stage build to keep the production image slim and secure.

Stage 1 (Build): Uses Maven to compile the .jar.

Stage 2 (Runtime): Uses jdk jammy (Alpine-based) and includes the New Relic Java Agent.

User: Runs as a non-root user appuser for security.

Health checks added

New Relic Integration: The agent is included in the ./newrelic folder and passed via the JAVA_OPTS environment variable in the ECS Task Definition: -javaagent:/usr/local/newrelic/newrelic.jar
