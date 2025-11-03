# Module 5: Feature Extraction Documentation

## Overview
This document provides a detailed explanation of the implementation in `M5_Features.ipynb`, which performs feature extraction on tumor masks using computer vision techniques.

## Implementation Structure

### 1. Imports and Constants (Section 1)
- `cv2`: OpenCV for contour detection and image processing
- `numpy`: Array operations and numerical computations
- `matplotlib`: Visualization of results
- `pathlib`: Path handling for file operations
- `csv/json`: Data storage formats
- `datetime`: Timestamp generation

Key constants:
- `DEFAULT_PREDICTED_MASK_DIR`: Default directory for input masks
- `OUTPUT_FEATURES_CSV`: Default output CSV file path
- `VIS_OUTPUT_DIR`: Directory for visualization outputs
- Visualization colors defined in BGR format

### 2. Mask Loading Functions (Section 2)
`load_mask(mask_path, threshold=127)`:
- Loads grayscale mask from file
- Ensures binary output (0/255 values)
- Includes error handling for file not found/read errors

`find_example_mask(mask_dir)`:
- Helper to locate sample mask files
- Searches for '*_tumor_mask.png' pattern
- Falls back to any PNG if specific pattern not found

### 3. Area and Perimeter Computation (Section 3)
`compute_area_perimeter(mask)`:
- Uses `cv2.findContours` to detect mask boundaries
- Finds external contours only (RETR_EXTERNAL)
- Selects largest contour by area
- Returns:
  - Contour points array
  - Area in pixels²
  - Perimeter in pixels

### 4. Centroid Computation (Section 4)
`compute_centroid(mask, contour)`:
- Primary method: Uses image moments via `cv2.moments`
- Fallback: Contour centroid if image moments fail
- Returns (x,y) coordinates of centroid
- Handles edge cases where moments are zero

### 5. Visualization Functions (Section 5)
`visualize_results(mask, contour, centroid, out_name, figsize)`:
- Draws contour in green
- Marks centroid with blue circle
- Adds coordinate text in red
- Saves visualization to file if out_name provided
- Uses matplotlib for display

### 6. Data Storage Functions (Section 6)
`save_features_csv(csv_path, records)`:
- Appends feature records to CSV
- Creates header if file doesn't exist
- Fields: filename, area, perimeter, centroid_x/y, timestamp

`save_features_json(json_path, records)`:
- JSON alternative for data storage
- Pretty-printed with indentation

### 7. Testing and Demo (Section 7)
`_basic_assertions(mask, contour, area, centroid)`:
- Validates mask properties
- Checks numeric constraints
- Verifies centroid within bounds

`_create_synthetic_mask(shape)`:
- Creates test mask with ellipse and circle
- Used when no real masks available

`demo_run(predicted_mask_path)`:
- Complete pipeline test
- Falls back to synthetic data if needed
- Includes visualization and data storage

### 8. Batch Processing (Section 8)
`process_all_masks(mask_root, output_csv, pattern)`:
- Processes multiple masks recursively
- Saves results to single CSV
- Includes progress reporting
- Error handling per file

### 9. Main Orchestrator (Section 9)
`run_module5(mask_path, mask_dir, batch, output_csv, visualize)`:
- Single entry point for all operations
- Three modes:
  1. Demo (synthetic mask)
  2. Single mask processing
  3. Batch processing
- Returns feature records
- Controls visualization

## Usage Examples
```python
# Demo with synthetic mask:
demo_rec = run_module5()

# Process single mask:
run_module5(mask_path=Path('kmeans_tumor_masks/Glioma/1841_tumor_mask.png'))

# Batch processing:
run_module5(batch=True, mask_dir=Path('kmeans_tumor_masks'), output_csv=Path('m5_features.csv'))
```

## Output Formats

### CSV Output
Each row contains:
- filename: Source mask name
- area: Area in pixels²
- perimeter: Perimeter in pixels
- centroid_x: X-coordinate of centroid
- centroid_y: Y-coordinate of centroid
- timestamp: UTC timestamp of processing

### Visualization Output
- Original mask with green contour
- Blue centroid marker
- Coordinate text annotation
- Saved as PNG in visualization directory

## Error Handling
- File not found handling
- Invalid mask format detection
- Graceful fallback for moment calculation
- Per-file error handling in batch mode

## Dependencies
- opencv-python-headless
- numpy
- matplotlib
- Python 3.x

## Citations
Implementation based on:
- CV Analysis.pdf
- 7.Binary image analysis.pdf