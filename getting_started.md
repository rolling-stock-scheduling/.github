# RSSched Getting Started

In the following you will find a step by step instruction how to use the RSSched algorithm.

As a first step we create a project directory `rssched`.

```bash
mkdir rssched
cd rssched
```

## 1. MATSim-Client

First we produce an input file for the solver from a MATSim run output.

1. clone the `rssched-matsim-client` repository:
   
   ```bash
   git clone git@github.com:rolling-stock-scheduling/rssched-matsim-client.git
   cd rssched-matsim-client
   ```

2. compile the project with maven (and download it if needed):
   
   ```bash
   chmod +x ./mvnw
   ./mvnw install
   ```
   
   This creates `target/rssched-matsim-client-0.2.0-SNAPSHOT.jar`.
   
   TODO: Alternativ können wir auch das .jar zum git hinzufügen.

3. copy you MATSim simulation run output into the `rssched/matsim_run` directory. As an example you can use the publicly available `kelheim-v3.0-25pct` instance from [https://github.com/matsim-scenarios/matsim-kelheim](https://github.com/matsim-scenarios/matsim-kelheim):
   
   ```bash
   cd ~/rssched
   mkdir matsim_run
   cd matsim_run
   wget -r -np -nH --cut-dirs=10 https://svn.vsp.tu-berlin.de/repos/public-svn/matsim/scenarios/countries/de/kelheim/kelheim-v3.0/output/25pct/
   cd ~/rssched/rssched-matsim-client
   ```

4. rename your MATSim output config file into `matsim_output_config.xml` and copy it to the `matsim_run` directory. You can also use our example instance:
   
   ```bash
   cp integration-test/input/de/kelheim/kelheim-v3.0/25pct/kelheim-v3.0-25pct.output_config.xml ../matsim_run/matsim_output_config.xml 
   ```

5. create a solver input via the small `run.java` script.
   
   ```bash
   java -cp .:target/rssched-matsim-client-0.2.0-SNAPSHOT.jar run.java
   ```
   
   TODO: Hier bekommen ich den folgenden Fehler:
   
   ```
   run.java:16: error: cannot access Scenario
                .setFilterStrategy(scenario -> {
                                   ^
   class file for org.matsim.api.core.v01.Scenario not found
   1 error
   error: compilation failed
   ```

## 2. Solver

Next, we start the solver-server and send the generated solver-input to obtain a rolling stock schedule.

1. clone the `rssched-solver` repository into the `rssched` directory:  
   
   ```bash
   cd ~/rssched
   git clone git@github.com:rolling-stock-scheduling/rssched-solver.git
   cd rssched-solver
   ```

2. install the docker engine: https://docs.docker.com/engine/install/

3. building the docker image (from the main directory):
   
   ```bash
   docker build --tag eth_scheduling_image .
   ```
   
   TODO: Anstelle können wir auch das image direkt bereitstellen.

4. loading the image and running the server for the first time (removes old container of the same name):
   
   ```bash
   docker run --rm --env RAYON_NUM_THREADS=16 --publish 3000:3000 --name eth_scheduling_server eth_scheduling_image
   ```
   
   the server can use 16 threads and answers on port 3000.

5. open a second terminal and use `curl` to send a POST request to the server containing the `solver-input.json` and creating an `schedule.json`:
   
   ```bash
   cd ~/rssched
   curl -X POST -H "Content-Type: application/json" -d @solver-input.json http://localhost:3000/solve > schedule.json
   ```
   
   TODO: hier den richtigen Namen für den solver-input.json wählen.

6. stopping the docker container:
   
   ```bash
   docker stop eth_scheduling_server
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
