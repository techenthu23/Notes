What is Graphviz?

Graphviz is an open-source graph visualization software used to represent structural information as diagrams of abstract graphs and networks. It uses the DOT language to describe graphs in a textual format, which can then be rendered into graphical formats like PNG, PDF, or SVG.


---

Basic Components (Building Blocks)

1. Nodes: Represent entities in the graph.

Syntax:

node_name [label="Custom Label"];

Example:

A [label="Node A"];



2. Edges: Represent relationships between nodes.

Directed Edge (with arrow):

A -> B;

Undirected Edge:

A -- B;



3. Graphs:

Directed Graph: Edges have directions (arrows).

digraph G {
    A -> B;
}

Undirected Graph: Edges do not have directions.

graph G {
    A -- B;
}



4. Attributes: Customize the appearance of nodes, edges, or the graph.

Example:

A [color=red, shape=box];
B [color=blue, style=dotted];





---

Basic Example

DOT File:

digraph Example {
    A [label="Start"];
    B [label="Middle"];
    C [label="End"];
    A -> B;
    B -> C;
}

Render the Graph:

1. Save the file as example.dot.


2. Generate an image:

dot -Tpng example.dot -o example.png




---

Use Cases of Graphviz

1. Dependency Graphs:

Visualize software dependencies, e.g., RPM package dependencies.

Example:

digraph Dependencies {
    PackageA -> PackageB;
    PackageB -> PackageC;
}



2. Flowcharts:

Represent workflows or processes.

Example:

digraph Workflow {
    Start -> Process1 -> End;
}



3. Organizational Charts:

Illustrate hierarchy or team structures.

Example:

digraph OrgChart {
    CEO -> Manager1;
    CEO -> Manager2;
    Manager1 -> Employee1;
}



4. Network Topologies:

Visualize computer networks, servers, or cloud architecture.

Example:

graph Network {
    Router -- Switch;
    Switch -- PC1;
    Switch -- PC2;
}



5. State Machines:

Describe state transitions in systems.

Example:

digraph StateMachine {
    Idle -> Processing;
    Processing -> Completed;
    Processing -> Error;
}





---

Benefits of Graphviz

Simple text-based format (DOT language) for creating graphs.

Generates various formats like PNG, PDF, or SVG.

Supports complex graphs with ease.



---

Example in Action

Visualizing a Simple Workflow

digraph Workflow {
    rankdir=LR; // Layout direction: Left to Right
    Start [shape=circle, color=green];
    Process [shape=box, label="Processing"];
    End [shape=doublecircle, color=red];

    Start -> Process -> End;
}

Render:

dot -Tpng workflow.dot -o workflow.png

This will produce a graph where "Start" leads to "Processing," which leads to "End."


---





Using Graphviz to parse and visualize RPM dependencies involves generating a dependency tree and converting it into a format compatible with Graphviz (e.g., DOT format). Here’s how you can do it:


---

Steps to Use Graphviz for RPM Dependencies

1. Install Required Tools

Ensure you have the following installed on your system:

Graphviz: To generate the graph.

sudo yum install graphviz

repoquery (from yum-utils) or dnf repoquery: To fetch dependencies.

sudo yum install yum-utils

or

sudo dnf install dnf-plugins-core



---

2. Generate Dependency Data

Use repoquery or dnf to get the dependency tree of a package.

Example:

repoquery --tree-requires <package_name>

This outputs the dependencies in a tree-like structure.



---

3. Convert Dependency Data to DOT Format

Write a script to parse the output and convert it into the DOT format. Here’s an example script:

Script Example (Bash + AWK):

#!/bin/bash

PACKAGE=$1
OUTPUT="${PACKAGE}_dependencies.dot"

# Get dependency tree
repoquery --tree-requires "$PACKAGE" | awk '
BEGIN { print "digraph dependencies {" }
{
    if ($1 ~ /^[A-Za-z0-9\-]+/) {
        main = $1
    } else if ($1 ~ /^[| ]+\\-[^\\-]/) {
        dep = $NF
        gsub("\\.[a-z0-9]+$", "", dep)  # Remove architecture suffix
        print "\"" main "\" -> \"" dep "\";"
    }
}
END { print "}" }
' > "$OUTPUT"

echo "DOT file generated: $OUTPUT"

Save this script as generate_dependencies.sh and make it executable:

chmod +x generate_dependencies.sh

Run it for a specific package:

./generate_dependencies.sh httpd



---

4. Generate the Graph

Use dot (Graphviz) to convert the DOT file into an image or PDF.

Command to Generate a PNG:

dot -Tpng -o dependencies.png httpd_dependencies.dot

Command to Generate a PDF:

dot -Tpdf -o dependencies.pdf httpd_dependencies.dot



---

5. View the Graph

Open the generated PNG or PDF file to view the visualized dependency graph.


---

Example Workflow

1. Generate the dependency data:

repoquery --tree-requires httpd


2. Convert it to DOT format using the script.


3. Generate the graph:

dot -Tpng -o httpd_dependencies.png httpd_dependencies.dot


4. Open the graph to see a structured visualization of all dependencies.




---

Complex Graphviz Example: Visualizing a Microservices Architecture

Below is an example of a more complex graph that represents a microservices architecture with dependencies between services and external systems.


---

DOT File Example

digraph MicroservicesArchitecture {
    rankdir=LR; // Layout from Left to Right
    graph [splines=polyline, nodesep=1.0, ranksep=1.5];

    // Define nodes for microservices
    ServiceA [label="Service A\n(API Gateway)", shape=box, style=filled, color=lightblue];
    ServiceB [label="Service B\n(User Service)", shape=ellipse, style=filled, color=lightgreen];
    ServiceC [label="Service C\n(Order Service)", shape=ellipse, style=filled, color=lightyellow];
    ServiceD [label="Service D\n(Inventory Service)", shape=ellipse, style=filled, color=lightpink];

    // Define external systems
    Database [label="Database", shape=cylinder, style=filled, color=lightgray];
    Queue [label="Message Queue", shape=box, style=dashed, color=orange];

    // Define edges (dependencies)
    ServiceA -> ServiceB [label="auth"];
    ServiceA -> ServiceC [label="order"];
    ServiceC -> ServiceD [label="inventory"];
    ServiceC -> Database [label="write"];
    ServiceD -> Database [label="read"];
    ServiceC -> Queue [label="publish"];
    Queue -> ServiceD [label="consume"];

    // Group related services
    subgraph cluster_Backend {
        label = "Backend Services";
        style = dashed;
        color = lightgrey;
        ServiceB;
        ServiceC;
        ServiceD;
    }
}


---

Automated Way to Generate Graphviz Graphs

If you need to automate the generation of complex graphs like the one above, you can use Python with the graphviz library or create the DOT file dynamically from a script.

1. Install the Graphviz Library for Python

pip install graphviz

2. Python Script to Automate Graph Generation

Here’s a Python script to generate the above microservices architecture graph dynamically:

from graphviz import Digraph

# Initialize the graph
graph = Digraph("MicroservicesArchitecture", format="png")
graph.attr(rankdir="LR", splines="polyline", nodesep="1.0", ranksep="1.5")

# Define nodes
services = {
    "ServiceA": {"label": "Service A\n(API Gateway)", "shape": "box", "color": "lightblue"},
    "ServiceB": {"label": "Service B\n(User Service)", "shape": "ellipse", "color": "lightgreen"},
    "ServiceC": {"label": "Service C\n(Order Service)", "shape": "ellipse", "color": "lightyellow"},
    "ServiceD": {"label": "Service D\n(Inventory Service)", "shape": "ellipse", "color": "lightpink"},
    "Database": {"label": "Database", "shape": "cylinder", "color": "lightgray"},
    "Queue": {"label": "Message Queue", "shape": "box", "style": "dashed", "color": "orange"},
}

# Add nodes to the graph
for service, attrs in services.items():
    graph.node(service, **attrs)

# Define edges (dependencies)
dependencies = [
    ("ServiceA", "ServiceB", "auth"),
    ("ServiceA", "ServiceC", "order"),
    ("ServiceC", "ServiceD", "inventory"),
    ("ServiceC", "Database", "write"),
    ("ServiceD", "Database", "read"),
    ("ServiceC", "Queue", "publish"),
    ("Queue", "ServiceD", "consume"),
]

for src, dest, label in dependencies:
    graph.edge(src, dest, label=label)

# Add cluster for grouping
with graph.subgraph(name="cluster_Backend") as backend:
    backend.attr(label="Backend Services", style="dashed", color="lightgrey")
    backend.node("ServiceB")
    backend.node("ServiceC")
    backend.node("ServiceD")

# Save and render the graph
graph.render("microservices_architecture", cleanup=True)


---

Output

When the script is run, it generates a PNG image (microservices_architecture.png) with the following structure:

Service A interacts with other services.

A Database and Message Queue connect services.

Services grouped into a "Backend Services" cluster.



---

Advantages of Automation

Easily modify nodes, edges, and attributes programmatically.

Dynamically generate graphs based on data (e.g., from APIs, JSON files, or database queries).

Reusability for various domains like microservices, network topologies, or organizational charts.


Here’s how you can define each flowchart symbol in Graphviz using the DOT language:


---

1. Oval (Terminator)

Definition:

Start [shape=ellipse, label="Start"];
End [shape=ellipse, label="End"];

Graphviz Attribute:

shape=ellipse


Example: Represents the start or end of a flowchart.



---

2. Rectangle (Process)

Definition:

Process1 [shape=box, label="Process Step 1"];
Process2 [shape=box, label="Process Step 2"];

Graphviz Attribute:

shape=box


Example: Represents tasks or operations.



---

3. Diamond (Decision)

Definition:

Decision [shape=diamond, label="Decision?"];

Graphviz Attribute:

shape=diamond


Example: Represents a decision point in the process.



---

4. Arrow (Connector)

Definition:

Start -> Process1;
Process1 -> Decision;

Graphviz Attribute:

No specific shape; arrows are created by linking nodes.


Example: Shows the flow between steps.



---

5. Parallelogram (Input/Output)

Definition:

Input [shape=parallelogram, label="Enter Data"];
Output [shape=parallelogram, label="Display Output"];

Graphviz Attribute:

shape=parallelogram
(Graphviz doesn’t natively support parallelogram, but you can approximate it with style attributes.)


Alternative with custom approximation:

Input [shape=box, label="Enter Data", style=rounded];



---

6. Circle (Connector/Off-Page Connector)

Definition:

ConnectorA [shape=circle, label="A"];
ConnectorB [shape=circle, label="B"];

Graphviz Attribute:

shape=circle


Example: Links different parts of a flowchart or pages.



---

7. Hexagon (Preparation)

Definition:

Preparation [shape=hexagon, label="Prepare Data"];

Graphviz Attribute:

shape=hexagon


Example: Used for preparation or initialization.



---

8. Cloud (Online Data/Storage)

Definition:

Cloud [shape=cloud, label="Cloud Storage"];

Graphviz Attribute:

shape=cloud (Requires external styles; otherwise, approximate it using ellipse.)


Alternative with approximation:

Cloud [shape=ellipse, label="Cloud Storage"];



---

9. Cylinder (Database)

Definition:

Database [shape=cylinder, label="Database"];

Graphviz Attribute:

shape=cylinder


Example: Represents a database.



---

10. Double Rectangle (Sub-Process)

Definition:

SubProcess [shape=box, peripheries=2, label="Reusable Process"];

Graphviz Attribute:

shape=box

peripheries=2 (Creates a double border)


Example: Represents a sub-process.



---

Complete Example

Here’s a Graphviz example combining all symbols into a flowchart:

digraph Flowchart {
    rankdir=TB; // Top-to-Bottom layout
    
    // Define nodes
    Start [shape=ellipse, label="Start"];
    Input [shape=parallelogram, label="Enter Data"];
    Process [shape=box, label="Process Data"];
    Decision [shape=diamond, label="Valid?"];
    Output [shape=parallelogram, label="Display Result"];
    End [shape=ellipse, label="End"];
    Database [shape=cylinder, label="Database"];
    SubProcess [shape=box, peripheries=2, label="Reusable Process"];
    Connector [shape=circle, label="Connector"];
    Cloud [shape=ellipse, label="Cloud Storage"];

    // Define edges
    Start -> Input;
    Input -> Process;
    Process -> Decision;
    Decision -> Output [label="Yes"];
    Decision -> End [label="No"];
    Output -> Database;
    Database -> SubProcess;
    SubProcess -> Cloud;
    Cloud -> Connector;
    Connector -> End;
}


---

Render the Flowchart

1. Save the DOT file as flowchart.dot.


2. Render with Graphviz:

dot -Tpng flowchart.dot -o flowchart.png


3. Open the flowchart.png to see the visual representation.

