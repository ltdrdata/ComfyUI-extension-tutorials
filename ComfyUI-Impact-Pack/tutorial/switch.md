# Switch (Any) / InversedSwitch (Any)

## Switch (Any)

This node is used to select one output from multiple inputs when executing a workflow.

* It receives multiple `input?` inputs and outputs a single one selected by `select`.
* `input?` can be of any type, and the output type is determined by the input type.

## InversedSwitch (Any)

This node is used to select and execute different types of sub-workflows for a single input.

* It can connect multiple output lines for a single input, and only outputs through the one selected by `select`.
* `input` can be of any type, and the type of `output?` is determined by the type of `input`.

## sel_mode

* `select_on_prompt`: Determines the selection by `select` statically before the workflow is executed.
    * This method must be used in ComfyUI versions prior to v0.0.9 due to limitations in dynamically changing the workflow execution structure.
    * It operates by virtually disconnecting node connections before the workflow is executed.
    * In this mode, connecting node outputs other than ImpactInt or Primitive nodes to the `select` input will cause malfunction.
    * In the following workflow, you can selectively execute a workflow using either Canny or OpenPose Pose depending on the select value.

![static](select_on_prompt.png)

* `select_on_execution`: Determines the selection by `select` dynamically during workflow execution.
    * This works properly only in ComfyUI versions after Aug. 16, 2024 (v0.0.9). Earlier versions will generate errors due to execution structure limitations.
    * In this mode, you can connect any integer output node to the `select` input.
    * Note: Normal operation is not guaranteed if the sub-workflow goes beyond the control range of the switch.
        * In the workflow below, issues arise if the outputs of Canny and OpenPose Pose nodes are connected to Preview Image.
        * This is because those nodes must be executed due to Preview Image, regardless of the Switch's control.

![dynamic](select_on_execution.png)
