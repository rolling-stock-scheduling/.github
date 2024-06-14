# RSSched Getting Started

In the following you will find a step-by-step-instruction how to use the RSSched algorithm.

To keep your home clean create a project directory `rssched`.

```bash
mkdir rssched
cd rssched
```

## 1. Start Solver-Server

First, we start the solver-server.

1. clone the `rssched-solver` repository into the `rssched` directory:  
   
   ```bash
   git clone git@github.com:rolling-stock-scheduling/rssched-solver.git
   cd rssched-solver
   ```

2. install the docker engine: https://docs.docker.com/engine/install/

3. building the docker image (from the `rssched-solver`-directory):
   
   ```bash
   docker build --tag eth_scheduling_image .
   ```

4. loading the image and running the server for the first time (removes old container of the same name):
   
   ```bash
   docker run --rm --env RAYON_NUM_THREADS=16 --publish 3000:3000 --name eth_scheduling_server eth_scheduling_image
   ```
   
   Here, the server can use 16 threads and answers on port 3000.

## 2. Run MATSim-Client

Next, we generate a solver-request from a MATSim run output and send it to the solver-server.

1. Open a new terminal, navigate to `rssched` and clone the `rssched-matsim-client` and the `rssched-solver` repository:
   
   ```bash
   cd ~/rssched
   git clone git@github.com:rolling-stock-scheduling/rssched-matsim-client.git
   cd rssched-matsim-client
   ```

2. Put your MATSim run output files, including
   
   - `*.output_config.xml`
   
   - `*.output_events.xml.gz`
   
   - `*.output_network.xml.gz`
   
   - `*.output_transitSchedule.xml.gz`
   
   - `*.output_transitVehicles.xml.gz`
     
     together with the config file containing the solver parameters
   
   - `*.rssched_request_config.xlsx`
   
   into the directory that is specified next to the `matsimInputDirectory` field on the first page of `*.rssched_request_config.xlsx`.
   
   As an example you can use the Kelheim instance that is located in `example_instance_kelheim/`.
   
   Since the `kelheim-v3.0-25pct.output_events.xml.gz` is too large for git use the following command to download them from the official repository or use this [link](https://svn.vsp.tu-berlin.de/repos/public-svn/matsim/scenarios/countries/de/kelheim/kelheim-v3.0/output/25pct/kelheim-v3.0-25pct.output_events.xml.gz) for a manual download.
   
   ```bash
   wget https://svn.vsp.tu-berlin.de/repos/public-svn/matsim/scenarios/countries/de/kelheim/kelheim-v3.0/output/25pct/kelheim-v3.0-25pct.output_events.xml.gz
   mv kelheim-v3.0-25pct.output_events.xml.gz example_instance_kelheim/input/
   ```

3. Build and run the `rssched-matsim-client` via `mvn` (you can use the maven-wrapper `.mvnw` if maven is not installed) to generate a solver-request and send it to the solver-server:
   
   ```bash
   ./mvnw clean install -DskipTests -DskipITs
   ./mvnw exec:java -Dexec.args="example_instance_kelheim/input/kelheim-v3.0-25pct.rssched_request_config.xlsx"
   ```
   
   As soon as the request is sent to the server you can observe the output of the solver within the server terminal.
   
   The final output (the rolling stock schedule) can be found at ```example_instance_kehlheim/output/rssched_kelheim-v3.0-25pct/it_config/kelheim-v3.0-25pct.scheduler_response.json``` or what ever path is specified next to the `outputDirectory` on the first page of `*.rssched_request_config.xlsx`.
   
   For convenience, lets copy the schedule file to the `rssched` directory:
   
   ```bash
   cp example_instance_kelheim/output/rssched_kelheim-v3.0-25pct/it_config/it_config.kelheim-v3.0-25pct.scheduler_response.json ../schedule.json
   ```

## 3. Analysis

Finally, we visualize the schedule for analysis.

1. Clone the `rssched-analysis` repository:
   
   ```bash
   cd ~/rssched
   git clone git@github.com:rolling-stock-scheduling/rssched-analysis.git
   cd rssched-analysis
   ```

2. Install poetry a package manager for python: https://python-poetry.org/docs/

3. Install the dependencies for this project via:
   
   ```bash
   poetry install
   ```

4. Create the plots (which will open automatically in a browser):
   
   ```sh
   poetry run rssched-plot ../schedule.json
   ```

## 4. Clean Up

To stop the Docker-container running the solver-server use:

```bash
docker stop eth_scheduling_server
```

This may take a couple of seconds.

For removing the RSSched programs simple remove the `rssched` directory with

```bash
cd ~
rm -rf rssched
```
