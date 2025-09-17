# Crowd Simulation, Prediction, and Emergency Response System

This project provides a multi-faceted system for simulating crowd behavior, predicting future crowd movements using an LSTM model, calculating optimal paths through dense areas, and strategically placing emergency response units based on crowd density analysis.

It is composed of three main modules:

1.  **Crowd Simulation & Prediction**: A grid-based simulation of crowd movement with a predictive LSTM model that forecasts future crowd hotspots.
2.  **Density-Aware Pathfinding**: An A\* based pathfinding system that calculates the most efficient routes by avoiding high-density areas.
3.  **Emergency Placement & Resource Allocation**: A post-processing tool that analyzes heatmaps to determine optimal locations for emergency services and allocates resources based on crowd concentration.

-----

## 🎯 Motivation

Managing large crowds during events like music festivals, religious gatherings (such as the Kumbh Mela), sporting events, or public demonstrations is a critical challenge for organizers and public safety officials. Unmanaged crowd flow can lead to dangerous overcrowding, stampedes, delayed emergency response, and logistical breakdowns.

Traditional crowd management is often reactive, addressing problems only after they arise. This project is motivated by the need to shift from a reactive to a **proactive, data-driven approach to public safety**.

The core motivations are:

  * **Preventing Disasters**: By simulating crowd dynamics, we can identify potential bottlenecks and high-risk zones *before* an event even begins, allowing planners to optimize venue layouts, signage, and barrier placements.
  * **Enhancing Emergency Response**: In a chaotic crowd, getting help to where it's needed is a race against time. A standard GPS is ineffective. This system provides first responders with intelligent, density-aware routes to navigate through or around congested areas, drastically reducing response times.
  * **Optimizing Resource Deployment**: The placement of medical tents, security personnel, and ambulances is often based on static plans. This project provides a dynamic method to determine the most effective locations for these resources based on real-time or predicted crowd concentrations, ensuring that help is always close by.
  * **Anticipating Crowd Behavior**: By using an LSTM model to predict near-future crowd movements, authorities can stay one step ahead. They can pre-emptively clear pathways, deploy personnel to areas expected to become dense, and manage crowd flow more effectively.

Ultimately, this project serves as a proof-of-concept for an integrated command-and-control system that leverages simulation and AI to make large-scale public events safer for everyone.

-----

## 🚀 Features

  * **Crowd Simulation**: Simulates the movement of hundreds of agents on a customizable 2D grid with static obstacles.
  * **Heatmap Generation**: Generates and saves cumulative heatmaps that visualize crowd density and movement patterns over time.
  * **Predictive AI**: Utilizes an LSTM neural network to predict the next state of the crowd grid, trained and updated in real-time during the simulation.
  * **Accuracy Metrics**: Provides a detailed, real-time evaluation of the LSTM model's prediction accuracy across four key metrics: Overall, Position, Clustering, and Direction.
  * **Dynamic Pathfinding**: Implements a density-aware A\* algorithm to find the quickest path from a start to a goal, treating dense crowds as high-cost areas.
  * **Multiple Path Strategies**: Generates alternative routes using different strategies ('Density Avoiding', 'Edge Preferring', 'Direct Route') to provide options for navigation.
  * **Path Smoothing**: Refines the calculated paths using a line-of-sight algorithm to create more natural, direct routes.
  * **Automated Emergency Placement**: Uses DBSCAN clustering on heatmaps to identify crowd hotspots and strategically places emergency units on the borders of these high-density zones.
  * **Voronoi-based Resource Allocation**: Employs Voronoi diagrams to partition the area around placed units and allocates resources (e.g., personnel, equipment) proportionally to the crowd density in each unit's zone.
  * **Rich Visualization**: Provides clear, real-time visualizations for the simulation, heatmaps, pathfinding results, and final emergency placements.

-----

## 🛠️ How It Works

The project is broken down into distinct but interconnected modules.

### 1\. Crowd Simulation & Prediction (`main.py`)

This is the core of the project. It initializes a grid with a set number of "people" (agents) and obstacles.

  * **Simulation Loop**: In each step, every agent decides its next move based on a simple rule: move to an adjacent empty cell that has the most neighboring agents. This encourages the formation of clusters and organic crowd movement.
  * **Heatmap Generation**: A cumulative heatmap is updated with each step, incrementing the value of a cell every time an agent occupies it. This creates a long-term record of high-traffic areas. These heatmaps are saved as PNG images in the `heatmaps/` directory.
  * **LSTM Prediction**: After an initial number of steps (`LSTM_START_STEP`), an LSTM model is trained on the sequence of past grid states. From that point on, it predicts the grid state for the next step. The model is continuously updated with new data as the simulation progresses.
  * **Visualization**: The script uses `matplotlib` to display the live simulation, the evolving heatmap, and a side-by-side comparison of the real grid versus the LSTM's prediction, complete with accuracy scores.

### 2\. Density-Aware Pathfinding (`pathfinding_system.py`)

This module is called at the end of the main simulation to demonstrate route planning in the final crowd state.

  * **Density Grid**: It first converts the simulation grid into a "density grid," where the cost of traversing a cell is proportional to the number of nearby people. This makes the A\* algorithm naturally avoid crowded areas.
  * **A* Algorithm*\*: It uses the A\* search algorithm to find the lowest-cost path from a start to a goal point, using the density grid to calculate movement costs.
  * **Path Strategies**: The `find_multiple_paths` function runs the A\* algorithm with slight modifications to its cost function to generate different types of paths (e.g., one that prefers staying close to walls, one that minimizes diagonal moves).
  * **Path Smoothing**: The best path is post-processed to remove redundant waypoints. It checks for a clear "line of sight" between points in the path and simplifies it into a series of straight lines where possible.

### 3\. Emergency Placement & Resource Allocation

This script works as a standalone tool that processes the heatmaps generated by the simulation.

  * **Cluster Detection**: It loads a heatmap image and uses the **DBSCAN** clustering algorithm to identify distinct, high-density regions or "hotspots."
  * **Border Placement**: Instead of placing units in the chaotic center of a crowd, the algorithm identifies the borders of these clusters. It places a specified number of "ambulances" on these borders, prioritizing points with the highest density while ensuring a minimum distance between each unit.
  * **Resource Allocation with Voronoi Diagrams**: After placing the units, a **Voronoi diagram** is generated. This partitions the map into cells where everything inside a cell is closest to the ambulance within it. The total crowd density inside each Voronoi cell is calculated, and resources are allocated to each ambulance on a scale of 1-10 based on this density. This ensures that units in busier areas get more support.
  * **Final Visualization**: The result is saved as a new image showing the heatmap, the placed units (sized according to their allocated resources), their resource score, and the Voronoi cell boundaries.

-----

## 📦 Installation & Setup

1.  **Prerequisites**:

      * Python 3.8+
      * `pip` package manager

2.  **Clone the Repository**:

    ```bash
    git clone https://github.com/your-username/your-repo-name.git
    cd your-repo-name
    ```

3.  **Install Dependencies**:
    Create a `requirements.txt` file with the following contents:

    ```
    numpy
    matplotlib
    scipy
    tensorflow
    opencv-python
    scikit-learn
    shapely
    ```

    Then, install them using pip:

    ```bash
    pip install -r requirements.txt
    ```

    *Note: The `matplotlib` backend might need to be set to `TkAgg` or `Qt5Agg` as shown in the code if you encounter display issues.*

-----

## 🏃‍♂️ Usage

### Running the Simulation and Pathfinding

To run the main crowd simulation, prediction, and pathfinding demonstration, simply execute the `main.py` script.

```bash
python main.py
```

  * Several `matplotlib` windows will open to show the simulation in real-time.
  * Heatmap images will be saved to the `heatmaps/` directory.
  * A final accuracy summary for the LSTM model will be printed in the console.

You can configure the simulation by changing the constants at the top of `main.py`:

  * `GRID_SIZE`: The size of the simulation grid.
  * `NUM_PEOPLE`: The number of agents in the simulation.
  * `STEPS`: The total number of steps to run.
  * `start`, `goal`: The coordinates for the pathfinding module.

### Running the Emergency Placement Analysis

This script processes the heatmaps generated by `main.py`.

1.  Ensure the `heatmaps/` directory exists and contains heatmap images.
2.  Run the ambulance placement script (assuming you've named it `ambulance_placement.py`).

<!-- end list -->

```bash
python ambulance_placement.py
```

  * The script will process each heatmap in the `heatmaps` folder.
  * The output visualizations will be saved in the `output_placements/` directory.
  * Placement coordinates and resource allocations will be printed to the console.
