# Pipeline

## Definition

The Pipeline Editor is designed to assist developers in orchestrating a series of operations in a predefined order using PHP scripts. This approach facilitates problem-solving or the accomplishment of specific tasks, which is fundamentally the creation of an algorithm.

The tool enables this to be done in a visual manner, where developers can arrange and connect nodes representing controllers in a graphical interface, without requiring direct coding for each step of the operation.

Developers can define the flow of the application simply by connecting these nodes, significantly reducing development time and improving accuracy by explicitly visualizing how data moves through the application.

By testing the created pipelines, developers can observe the flow of data, adjust parameters, and optimize the performance of their solutions.

## Create a pipeline

### Add a controller

To add a controller in the form of a node to the pipeline, you must click on the '+' button at the top right (1). A list of controllers will appear on the right (2). You can search within this list (3) or apply a filter to your search (4), based on the type of controller (data or actions) or based on a package. You can also add a node representing a router (5). The router allows distributing the outputs of multiple controllers to other controllers.

<img src="/_assets/img/pipeline_add_controller.png" style="height : 200px;">

### Create a link

To create a link between two nodes, you must click on the button to the right of the source node (1), and then click on the button to the left of the target node (2).

<img src="/_assets/img/pipeline_create_link.png" style="height : 200px;">

### Edit an element

You can edit a node or a link by clicking on it.

When you click on a node representing a controller, a delete button (1) and a description of the node (2) appear next to it. On the right, you can change the name of the node or its description (3). You can also change the appearance of the node, such as the icon or the color, by going to the 'Appearance' tab (4). The 'Data' tab (5) allows you to view the input parameters of a controller as well as its description. You can enter the value of a parameter (6).

If the node is a router, you will see the delete button appear, and on the right, you will see a list of controllers from which it receives the output.

<img src="/_assets/img/pipeline_edit_node.png" style="height : 200px;">

When you click on a link, a delete button (1) appear next to it. On the right, you can link the output of a source node to the input of a target node (2).

If the link has a router node as the target, there will be nothing displayed in the input column.

<img src="/_assets/img/pipeline_edit_link.png" style="height : 200px;">

### Save, load and run

You can save (1) and load (2) a pipeline. Don't forget to provide a name for it (2). Yon can also run a pipeline (4).

<img src="/_assets/img/pipeline_save_load_run.png" style="height : 200px;">

When you click on the 'run' button, a modal window appears. Once the pipeline is launched (1), logs are displayed to keep you informed about the progress of the execution (2).

<img src="/_assets/img/pipeline_modal_run.png" style="height : 200px;">