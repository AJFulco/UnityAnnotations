# Unity Annotation Tool

## Overview
The Unity Annotation Tool is a custom editor tool designed to enable easy annotation within the Unity Editor. It allows developers to create, edit, and erase annotations directly in the Scene view, facilitating a more streamlined and efficient workflow for collaborative projects.

## Features
- **Draw Mode**: Draw lines directly in the Scene view.
- **Text Mode**: Place text annotations in the Scene view.
- **Eraser Mode**: Erase existing annotations.
- **Undo Last Stroke**: Undo the last annotation added.
- **Clear Annotations**: Remove all annotations from the Scene.
- **Brush Size and Color**: Customize the brush size and color for drawing annotations.
- **Annotations Layer**: Ensure annotations are always visible and do not interfere with other scene objects.

## Installation
1. **Clone the Repository**: Clone the repository containing the Unity Annotation Tool to your local machine.
2. **Import into Unity**: Open your Unity project and import the cloned repository. You can do this by copying the files into your `Assets` folder or using the Unity Package Manager.

## Usage
1. **Open the Annotation Tool**: In the Unity Editor, go to `Tools > Annotation Tool` to open the tool window.
2. **Activate the Tool**: Toggle the "Activate Annotation Tool" button to enable or disable the annotation tool.
3. **Select Mode**: Choose between Draw Mode, Text Mode, and Eraser Mode by clicking the respective buttons.
   - **Draw Mode**: Click and drag in the Scene view to draw lines. Adjust brush size and color in the tool window.
   - **Text Mode**: Click in the Scene view to place a text annotation. Enter the desired text in the tool window.
   - **Eraser Mode**: Click and drag in the Scene view to erase annotations.
4. **Manage Annotations**:
   - **Undo Last Stroke**: Click the "Undo Last Stroke" button to remove the last annotation added.
   - **Clear Annotations**: Click the "Clear Annotations" button to remove all annotations from the scene.

## Notes
- **Non-Interference**: The annotation tool is designed to not interfere with other Unity tools, such as the tile palette tool. Ensure the tool is deactivated when not in use to avoid conflicts.
- **Annotations Layer**: The tool uses a dedicated "Annotations" layer to ensure annotations are rendered on top of other scene objects. Make sure this layer is properly configured in your project.

## Contributing
Contributions to improve the tool are welcome. Feel free to fork the repository and submit pull requests with enhancements or bug fixes.

## License
This project is licensed under the MIT License. See the LICENSE file for more details.

## Contact
For any questions or support, please contact [tastiegamesofficial@gmail.com].
