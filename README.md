# JIT Pothole Detection System

A web-based infrastructure assessment application built with Flask, YOLOv8 (Ultralytics), and a custom Convolutional Neural Network (CNN) in TensorFlow. The system detects potholes in road images, measures the severity, generates visual heatmaps, and logs the geographic coordinates for maintenance prioritization.

## Project Structure

```text
JIT-POTHHOLE/
├── app.py                # Main Flask application for inference, web server, and API routes
├── train.py              # CNN model training script for pothole severity classification
├── best.pt               # Pre-trained YOLOv8 model for pothole detection (bounding boxes)
├── CNN.model/            # Pre-trained Keras/TensorFlow CNN model for severity classification
├── road_data.csv         # Database logging history of detected potholes and GPS coordinates
├── templates/
│   └── index.html        # Web interface frontend HTML
├── static/
│   ├── output/           # Stored processed images (Grad-CAM heatmaps, overlays, bounding boxes)
│   └── uploads/          # Temporary storage directory for user-uploaded raw images
├── data/                 # Directory containing categorized training data for CNN classification
├── test/                 # Sample images for testing the system
└── venv / myenv/         # Python virtual environments (Dependencies)
```

## Inputs

The project takes image data as input to extract analytics:
- **User Uploads:** Users can upload single or multiple images (`.jpg`, `.jpeg`, `.png`, `.bmp`, `.webp`) via the `index.html` file in the Web UI.
- **Folder Uploads:** The system can also batch-process an entire folder of images through the `/analyze_folder` API endpoint.
- **AI Processing Inputs:**
  - **YOLOv8** component receives the unaltered uploaded image.
  - **CNN Classifier** receives an isolated segment of the image normalized and resized to `50x50` pixels.
- **Network Extracted Input:** The application retrieves real-time geographic data (City, Latitude, Longitude) via `http://ip-api.com`.

## Outputs

The system generates several physical artifacts and data points resulting from the detection analysis:

### 1. Processed Visual Artifacts (`/static/output/`)
- **Detection Image (`*_det.jpg`):** Original image overlaid with a highlighted yellow bounding box indicating the precise location of the pothole structure detected by the YOLO model.
- **Grad-CAM Heatmap (`*_heatmap.jpg`):** Visual depiction of model decision-making focus, mapping high-activation gradients in "jet" colorscale.
- **Overlap Image (`*_overlay.jpg`):** An intuitive blend of the original image with the generated Grad-CAM heatmap.

### 2. Extracted Data Logs (`road_data.csv`)
Automatically logs processed data for dashboarding and maintenance review. Fields include:
- `Image`: The processed image filename.
- `City`, `Lat`, `Lon`: The location IP data retrieved for mapping purposes.
- `Severity`: Classified as `Moderate`, `Poor`, or `Severe` by the CNN component.
- `Conf`: The Confidence percentage of the CNN's classification.
- `Damage`: Numerical approximation of road surface structural damage based on the bounding box areas calculated from YOLO.
- `Timestamp`: Datetime record of the analysis.

### 3. API Response Responses (JSON)
For API integrations or frontend rendering, returns a complete payload:
```json
{
  "filename": "image.jpg",
  "pothole": true,
  "severity": "Moderate",
  "confidence": 98.5,
  "damage": 12.3,
  "city": "Current City",
  "lat": 44.5,
  "lon": -91.2,
  "timestamp": "YYYY-MM-DD HH:MM:SS"
}
```

## How to Run the Project

1. **Setup the Virtual Environment:** Open a terminal in the project directory and activate the python virtual environment.
   ```bash
   # Windows
   myenv\Scripts\activate
   ```
2. **Install Dependencies:** If you don't have them installed already, ensure you have packages like `flask`, `ultralytics`, `tensorflow`, `opencv-python`, and `matplotlib`.
   ```bash
   pip install -r requirements.txt  # If requirements exist, otherwise install via pip
   ```
3. **Run the Application Server:**
   ```bash
   python app.py
   ```
4. **Access the Open Dashboard:** 
   Open a web browser and go to `http://127.0.0.1:5000` or `http://localhost:5000` to interact with the frontend.
