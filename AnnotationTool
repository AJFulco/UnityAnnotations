using UnityEngine;
using UnityEditor;
using System.Collections.Generic;
using System.Linq;

//UNITY 2D ANNOTATION TOOL; BY A.J. FULCO @ TASTIE GAMES LLC. 06/09/2024
//VER 1.09

public class AnnotationTool : EditorWindow
{
    private enum Mode { None, Draw, Text, Eraser }
    private Mode currentMode = Mode.None;
    private bool isDrawing = false;
    private Vector3 startMousePosition;
    private LineRenderer currentLineRenderer;
    private GameObject annotationParent;
    private string annotationText = "";
    private Material lineMaterial;
    private int annotationLayer;
    private List<Vector3> points = new List<Vector3>();
    private Stack<GameObject> annotationStack = new Stack<GameObject>();

    private float brushSize = 0.1f;
    private Color brushColor = Color.red;
    private float eraserSize = 0.5f;
    private string sortingLayerName = "Annotations"; // Default sorting layer name
    private int sortingOrder = 0; // Default sorting order

    private bool annotationToolActive = false;

    [MenuItem("Tools/Annotation Tool")]
    public static void ShowWindow()
    {
        GetWindow<AnnotationTool>("Annotation Tool");
    }

    private void OnEnable()
    {
        SceneView.duringSceneGui += OnSceneGUI;
        EnsureAnnotationParentExists();
        CreateLineMaterial();
    }

    private void OnDisable()
    {
        SceneView.duringSceneGui -= OnSceneGUI;
    }

    private void EnsureAnnotationParentExists()
    {
        annotationLayer = LayerMask.NameToLayer("Annotations");
        if (annotationLayer == -1)
        {
            Debug.LogError("Layer 'Annotations' does not exist. Please add it in the Tags and Layers settings.");
            return;
        }

        annotationParent = GameObject.Find("Annotations");
        if (annotationParent == null)
        {
            annotationParent = new GameObject("Annotations");
            annotationParent.layer = annotationLayer;
            annotationParent.hideFlags = HideFlags.HideInHierarchy;
            EditorUtility.SetDirty(annotationParent);
        }
    }

    private void CreateLineMaterial()
    {
        // Create a basic material for the line renderer
        lineMaterial = new Material(Shader.Find("Hidden/Internal-Colored"));
        lineMaterial.hideFlags = HideFlags.HideAndDontSave;
        lineMaterial.shader.hideFlags = HideFlags.HideAndDontSave;
    }

    private void OnGUI()
    {
        GUILayout.Label("Annotation Tool", EditorStyles.boldLabel);

        annotationToolActive = GUILayout.Toggle(annotationToolActive, "Activate Annotation Tool", "Button");

        if (!annotationToolActive)
        {
            currentMode = Mode.None;
            return;
        }

        GUIStyle drawButtonStyle = new GUIStyle(GUI.skin.button);
        GUIStyle textButtonStyle = new GUIStyle(GUI.skin.button);
        GUIStyle eraserButtonStyle = new GUIStyle(GUI.skin.button);

        if (currentMode == Mode.Draw)
        {
            drawButtonStyle.normal.textColor = Color.green;
        }
        else
        {
            drawButtonStyle.normal.textColor = Color.white;
        }

        if (currentMode == Mode.Text)
        {
            textButtonStyle.normal.textColor = Color.green;
        }
        else
        {
            textButtonStyle.normal.textColor = Color.white;
        }

        if (currentMode == Mode.Eraser)
        {
            eraserButtonStyle.normal.textColor = Color.green;
        }
        else
        {
            eraserButtonStyle.normal.textColor = Color.white;
        }

        if (GUILayout.Button("Draw Mode", drawButtonStyle))
        {
            currentMode = currentMode == Mode.Draw ? Mode.None : Mode.Draw;
        }
        if (GUILayout.Button("Text Mode", textButtonStyle))
        {
            currentMode = currentMode == Mode.Text ? Mode.None : Mode.Text;
        }
        if (GUILayout.Button("Eraser Mode", eraserButtonStyle))
        {
            currentMode = currentMode == Mode.Eraser ? Mode.None : Mode.Eraser;
        }
        if (GUILayout.Button("Clear Annotations"))
        {
            ClearAnnotations();
        }

        if (GUILayout.Button("Undo Last Stroke"))
        {
            UndoLastStroke();
        }

        if (currentMode == Mode.Draw)
        {
            GUILayout.Label("Brush Size");
            brushSize = EditorGUILayout.Slider(brushSize, 0.01f, 1.0f);
            GUILayout.Label("Brush Color");
            brushColor = EditorGUILayout.ColorField(brushColor);
        }

        if (currentMode == Mode.Eraser)
        {
            GUILayout.Label("Eraser Size");
            eraserSize = EditorGUILayout.Slider(eraserSize, 0.1f, 2.0f);
        }
    }

    private void ClearAnnotations()
    {
        foreach (Transform child in annotationParent.transform)
        {
            DestroyImmediate(child.gameObject);
        }
        annotationStack.Clear();
        EditorUtility.SetDirty(annotationParent);
    }

    private void UndoLastStroke()
    {
        if (annotationStack.Count > 0)
        {
            GameObject lastAnnotation = annotationStack.Pop();
            DestroyImmediate(lastAnnotation);
            EditorUtility.SetDirty(annotationParent);
        }
    }

    private void OnSceneGUI(SceneView sceneView)
    {
        if (!annotationToolActive) return;

        EnsureAnnotationParentExists();
        if (annotationLayer == -1 || annotationParent == null) return;  // Exit if the layer is not set up properly or annotationParent is null

        HandleUtility.AddDefaultControl(GUIUtility.GetControlID(FocusType.Passive));
        Event e = Event.current;

        switch (currentMode)
        {
            case Mode.Draw:
                HandleDrawMode(e, sceneView);
                break;
            case Mode.Text:
                HandleTextMode(e, sceneView);
                break;
            case Mode.Eraser:
                HandleEraserMode(e, sceneView);
                break;
        }
    }

    private void HandleDrawMode(Event e, SceneView sceneView)
    {
        if (e.type == EventType.MouseDown && e.button == 0)
        {
            isDrawing = true;
            points.Clear();
            startMousePosition = e.mousePosition;
            GameObject lineObject = new GameObject("LineAnnotation");
            lineObject.transform.parent = annotationParent.transform;
            lineObject.layer = annotationLayer;

            currentLineRenderer = lineObject.AddComponent<LineRenderer>();
            currentLineRenderer.startWidth = brushSize;
            currentLineRenderer.endWidth = brushSize;
            currentLineRenderer.material = lineMaterial;
            currentLineRenderer.startColor = brushColor;
            currentLineRenderer.endColor = brushColor;
            currentLineRenderer.useWorldSpace = false;
            currentLineRenderer.numCapVertices = 10; // for rounded edges
            currentLineRenderer.sortingLayerName = sortingLayerName;
            currentLineRenderer.sortingOrder = sortingOrder;
            EditorUtility.SetDirty(lineObject);
            annotationStack.Push(lineObject);
        }

        if (e.type == EventType.MouseDrag && isDrawing)
        {
            Vector3 mousePosition = e.mousePosition;
            Vector3 worldPosition = HandleUtility.GUIPointToWorldRay(mousePosition).GetPoint(0);
            worldPosition.z = 0; // Ensure z position is always 0
            if (points.Count == 0 || Vector3.Distance(points[points.Count - 1], worldPosition) > 0.01f) // Adjust distance threshold for more responsive drawing
            {
                points.Add(worldPosition);
                currentLineRenderer.positionCount = points.Count;
                currentLineRenderer.SetPositions(points.ToArray());
                SceneView.RepaintAll(); // Repaint the scene view to see the drawing in real time
                EditorUtility.SetDirty(currentLineRenderer.gameObject);
            }
        }

        if (e.type == EventType.MouseUp && isDrawing)
        {
            isDrawing = false;
            points.Clear();
            currentLineRenderer = null;
        }
    }

    private void HandleTextMode(Event e, SceneView sceneView)
    {
        if (e.type == EventType.MouseDown && e.button == 0)
        {
            Vector3 worldPosition = HandleUtility.GUIPointToWorldRay(e.mousePosition).GetPoint(0);
            worldPosition.z = 0; // Ensure z position is always 0
            GameObject textObject = new GameObject("TextAnnotation");
            textObject.transform.position = worldPosition;
            textObject.transform.parent = annotationParent.transform;
            textObject.layer = annotationLayer;

            TextMesh textMesh = textObject.AddComponent<TextMesh>();
            textMesh.text = annotationText;
            textMesh.characterSize = 0.1f;
            textMesh.fontSize = 100;
            textMesh.GetComponent<Renderer>().sortingLayerName = sortingLayerName;
            textMesh.GetComponent<Renderer>().sortingOrder = sortingOrder;
            EditorUtility.SetDirty(textObject);
            annotationStack.Push(textObject);
        }

        Handles.BeginGUI();
        GUILayout.BeginArea(new Rect(10, 10, 200, 100));
        GUILayout.Label("Enter Annotation Text:");
        annotationText = GUILayout.TextField(annotationText, GUILayout.Height(50));
        GUILayout.EndArea();
        Handles.EndGUI();
    }

    private void HandleEraserMode(Event e, SceneView sceneView)
    {
        if (e.type == EventType.MouseDown && e.button == 0)
        {
            EraseAnnotations(e.mousePosition);
        }

        if (e.type == EventType.MouseDrag && e.button == 0)
        {
            EraseAnnotations(e.mousePosition);
        }
    }

    private void EraseAnnotations(Vector2 mousePosition)
    {
        Vector3 worldPosition = HandleUtility.GUIPointToWorldRay(mousePosition).GetPoint(0);
        worldPosition.z = 0; // Ensure z position is always 0

        List<GameObject> toRemove = new List<GameObject>();

        foreach (Transform child in annotationParent.transform)
        {
            if (Vector3.Distance(child.position, worldPosition) < eraserSize)
            {
                toRemove.Add(child.gameObject);
            }
        }

        foreach (GameObject obj in toRemove)
        {
            annotationStack = new Stack<GameObject>(annotationStack.Where(o => o != obj));
            DestroyImmediate(obj);
        }
        EditorUtility.SetDirty(annotationParent);
    }
}
