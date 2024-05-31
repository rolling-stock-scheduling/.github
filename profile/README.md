## Rolling Stock Scheduling (RSSched)

How will Switzerland’s mobility landscape look in 20 to 30 years? Undoubtedly, train service will continue to play a central role, but perhaps entirely new types of trains will operate on the Swiss rail network. Certainly, sophisticated optimization algorithms will ensure that future schedules are used as efficiently as possible by minimizing costs and emissions while satisfying the rising passenger demand.

To explore these questions, the Swiss Federal Railways (SBB) collaborated with the Institute of Operations Research (ETH Zürich) on a year-long research project to design highly efficient algorithms for optimizing rolling stock schedules. The key objectives of this project are to minimize the number of rolling stock vehicles for a given passenger demand and to minimize the total travel distance of deadhead trips (trips without passengers) while satisfying several constraints, such as maintenance regulations.

One of the most important tools for analyzing mobility scenarios is MATSim (Multi-Agent Transport Simulation), which provides a framework for large-scale agent-based transport simulations, combining modules for demand modeling, traffic flow simulation, re-planning, and output analysis. The railsim contribution within MATSim simulates realistic train operations, addressing aspects like spatial train expansion across multiple links, speed dynamics (acceleration/deceleration), and capacity effects at the resource level.

Based on this framework, the result of this successful collaboration consists of three software packages:

1) Rolling Stock Scheduling Optimization Algorithm (or "Solver") – This multi-layered algorithm, developed by ETH, is written in Rust to achieve the highest performance. An input instance, provided via a predefined interface, consists of location data, including travel times and distances for deadhead trips, routes and departures, depots with capacities, fleet type information, and maintenance slots and regulations. Powered by classical algorithms (e.g., network-simplex to solve a batch of min-cost-circulation problems) and newly developed local-search-based meta-heuristics, this solver produces a rolling stock schedule that:

- Covers all service trips to meet passenger demand
- Meets maintenance regulations
- Minimizes the number of train vehicles needed
- Minimizes the total distance traveled to reduce costs and emissions

2) MATSim Connector – SBB developed this pipeline algorithms written in Java to connect the simulation environment and the Solver. For analyzing future mobility scenarios in Switzerland, SBB uses MATSim simulations to create a realistic passenger demand. The pipeline transforms the standard output files of a MATSim simulation run into an input instance for the solver. As the solver input is a macroscopic view, multiple aspects must be transformed in this preprocessing step.

3) Visualization Tool – To analyze the output of the Solver, SBB developed a visualization tool written in Python. This tool not only allows for the analysis of a rolling stock schedule on an intuitive/visual basis but also provides a sequence of key performance indicators, such as the total deadhead trip distance, the load over the day, and vehicle utilization.

All three parts work seamlessly with each other and can be integrated into SBB's MATSim simulation environment. The code is well tested and documented, and several realistic large-scale instances were successfully tested and analyzed.

It is planned to use the railsim contribution to validate the feasibility of the calculated schedule solutions at an operational level with given infrastructure.
