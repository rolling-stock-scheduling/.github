# RSSched Getting Started

In the following you will find a step by step instruction how to use the RSSched algorithm.

As a first step we create a project directory `rssched`.

```bash
mkdir rssched
cd rssched
```

## 1. Start Solver-Server

Next, we start the solver-server and send the generated solver-input to obtain a rolling stock schedule.

1. clone the `rssched-solver` repository into the `rssched` directory:  
   
   ```bash
   git clone git@github.com:rolling-stock-scheduling/rssched-solver.git
   cd rssched-solver
   ```

2. install the docker engine: https://docs.docker.com/engine/install/

3. building the docker image (from the main directory):
   
   ```bash
   docker build --tag eth_scheduling_image .
   ```

4. loading the image and running the server for the first time (removes old container of the same name):
   
   ```bash
   docker run --rm --env RAYON_NUM_THREADS=16 --publish 3000:3000 --name eth_scheduling_server eth_scheduling_image
   ```
   
   the server can use 16 threads and answers on port 3000.

## 2. Run MATSim-Client

We can generate a solver-request from a MATSim run output and send it to the server.

1. open a new terminal, navigate to `rssched` and clone the `rssched-matsim-client` and the `rssched-solver` repository:
   
   ```bash
   cd ~/rssched
   git clone git@github.com:rolling-stock-scheduling/rssched-matsim-client.git
   cd rssched-matsim-client
   ```

2. put your MATSim run output files together with config.xlsx (where the solver parameters are specified) into a directory and use the `src/main/java/ch/sbb/rssched/Application.java` to generate a solver-request and send it to the solver-server.
   
   TODO insert the correct commands

#### Kehlheim-Example-Instance

Alternative to your own MATSim run you can use the integration test in `/src/test/java/ch/sbb/rssched/client/RsschedMatsimClientIT.java` to run the Kehlheim-example-instance. For this you can use maven by executing the `mvnw` binary file:

```bash
git checkout bugfix/move-integration-test
chmod +x ./mvnw
./mvnw verify -Dit.test=RsschedMatsimClientIT
```

   TODO: Remove checkout command as soon as bug fix is merged to main.

   This will take a while, as some part of the instance are downloaded. As soon as the request is sent to the server you can observe the output of the solver within the server terminal.

   The final output (rolling stock schedule) can be found at ```integration-test/output/de/kelheim/kelheim-v3.0/25pct/rssched_kelheim-v3.0-25pct/kelheim-v3.0-25pct.scheduler_response.json```

For convenience lets copy the file to the `rssched` directory

```bash
cp integration-test/output/de/kelheim/kelheim-v3.0/25pct/rssched_kelheim-v3.0-25pct/kelheim-v3.0-25pct.scheduler_response.json ../schedule.json
```

## 3. Analysis

Finally, we visualize the schedule for analysis.

1. clone the `rssched-analysis` repository
   
   ```bash
   cd ~/rssched
   git clone git@github.com:rolling-stock-scheduling/rssched-analysis.git
   cd rssched-analysis
   ```

2. install poetry a package manager for python: https://python-poetry.org/docs/

3. install the dependencies for this project via:
   
   ```bash
   poetry install
   ```

4. create the plots (which will open automatically in a browser):
   
   ```sh
   poetry run rssched-plot ../schedule.json
   ```

## 4. Stopping the Server

Stop the Docker-container running the solver-server via:

```bash
docker stop eth_scheduling_server
```
