# Crop Classification App

Crop Classification App is an application based on the Random Forest algorithm, intended for the classification of agricultural areas based on Sentinel satellite images. The project allows you to visualize the classification and generate plot of various types of crops for a given area. The training layer was made on the basis of crops in the village of Sławsk Wielki in July 2024.
Requirements

The project requires a Python virtual environment containing several libraries for data analysis and map visualization.

## Required steps to create a virtual environment

### 1. Create a virtual environment

In your project directory, run the command:

```bash
python3 -m venv cropenv
```
### 2. Activate the virtual environment

Windows:
```bash
cropenv\Scripts\activate
```

macOS/Linux:
```bash
source cropenv/bin/activate
```

### 3. Install the required packages
```bash
pip install -r requirements.txt
```
### 4. To add Virtual Environment to Jupyter Notebook: 
```bash
python -m ipykernel install --user --name=cropenv
```

# Application functionalities
## Main function: classify

The classify function allows you to classify an agricultural area based on given coordinates and returns a classification map for a specific area in the bounding box.

### Function arguments:

cords (required): Coordinates of the center of the bounding box to be classified, in the form [latitude, longitude].

generate_crops_plot (optional): True/False flag, default is False. Setting it to True generates a plot of crop area in selected years.

start_date (optional): Start date of the Sentinel image, default '2024-06-30'. It is best not to change to maintain consistency of results.

stop_date (optional): End date of the Sentinel image, default '2024-07-01'. Change not recommended.

cloudy_pixel_percentage (optional): The percentage of cloud content in the image, default is 100. This allows you to limit images with a lot of clouds, but to ensure classification accuracy it is best to leave this value unchanged.

### Examples of using the function

1. Basic call to the classifying function:

```python
Map = classify([52.68, 18.28])
```

The function will return a classification map for coordinates [52.68, 18.28].

2. Calling the function with generating a crop chart:

```python
Map, crop_plot = classify([52.68, 18.28], generate_crops_plot=True)
```

In this case, the function will return a map and the crop plot of crop area in particular years.

Displaying a graph of crop area

To see the crop area graph, execute the following command:

```python
generate_plot(crop_plot)
```

## Opis działania funkcji classify

The classify function works in two layers:

Bottom layer (SAR): SAR data from 2015-2023 is used for preliminary classification, for the designated growing season.

Top layer (SAR+optical): Classification based on the Sentinel image from the current year (2024) allows for up-to-date crop mapping.

Each layer uses a training model adapted to the region specification and data type (SAR or SAR+optical). Classification maps are created for each period, and when the generate_crop_plot is turned on, the function generates additional crop area data.
Legending the map

The resulting map includes a legend describing the crop classes, making it easier to interpret the classification results.
Additional information

Users can customize image dates and the cloudy_pixel_percentage parameter, although the default values ​​provide the best classification quality for the default region selected.

In case of problems with visualization, make sure that Jupyter Notebook has an active cropenv environment as the kernel.


## Accuracy function

The accurency function allows you to calculate and visualize the classification accuracy in a selected area. Creates a map with a mask showing where the classification model is working correctly, and a histogram showing the number of correctly and incorrectly classified pixels.

### Function arguments:

start_date (optional): Start date of the Sentinel image, default '2024-06-30'. Specifies the period from which the data used for accuracy calculations comes from. It is recommended to leave this default value.

stop_date (optional): End date of the Sentinel image, default '2024-07-01'. In combination with start_date, defines the time range of Sentinel data.

cloudy_pixel_percentage (optional): Percentage of the maximum amount of clouds in the image, default is 100. Changing this parameter allows you to select images with less clouds, but it is best to leave the default value.

### Example of using the function

To run the function and generate an accuracy map and histogram, use the following call:

```python
Map, hist = accuracy()
```

### The function returns:

Map: Map with layers showing:
crops_raster - original training data,
points - training points (invisible by default),
classified - model classification results,
accurency - a mask with correct and incorrect classification areas, where correctly classified pixels are marked and incorrectly classified pixels are not masked.

hist: A histogram showing the number of pixels classified correctly and incorrectly.

### Description of how the function works

Basemap Creation: Prepares a basemap for the area with coordinates [52.68, 18.28] used for visualization.

Preparing data for classification: Selects image data for a given area, applying settings for the region, including Sentinel optical images.

Calculating classification accuracy:
Creates an equal_mask that indicates correctly classified pixels (pixels where the classes in the classification image match the original crop raster).
Map adds an accurency mask to show the results of correct classification.

Histogram Generation: The histogram contains the number of pixels classified correctly and incorrectly, which allows you to evaluate the overall classification accuracy.

The histogram can be displayed immediately after calling the function or exported for further analysis.


## Classify_crop function

The classify_crop function allows you to visualize the frequency of occurrence of a selected crop based on historical and current data. It works by analyzing a selected area based on SAR and optical Sentinel data.

### Function arguments:

cords (required): Coordinates of the center of the area in the form of a bounding box [latitude, longitude].

crop_key (required): Name of the crop whose occurrence we want to investigate. Available crops are:
    'Rapeseed', 'Winter wheat', 'Sugar beet', 'Corn', 'Spring barley', 'Potatoes', 'Spring onion', 'Soil', 'Broccoli', 'Oats', 'Winter onion', ' Garlic', 'Trees', 'Pumpkin', 'Phacelia', 'Beans', 'Peas', 'Green peas', 'Winter barley', 'Dill', 'Alfalfa', 'Parsley', 'Rye with barley' , 'Celery', 'Soy', 'Grass', 'Rye'.

color (required): The color code in HEX format (e.g. 'b3573b') that will be used to visualize the selected crop on the map. The function generates lighter shades of this color to indicate different frequency levels.

start_date (optional): Start date for Sentinel data, default '2024-06-30'. For consistency of results, it is recommended not to change this parameter.

stop_date (optional): End date for Sentinel data, default '2024-07-01'. Again, it's best to leave the default value.

cloudy_pixel_percentage (optional): Maximum percentage of clouds in images. The default value is 100.

### Example of using the function

To generate a frequency map of the selected crop, make the following call:

```python
Map = classify_crop([52.68, 18.28], 'Sugar beet', 'b3573b')
```

The result will be a Map with a layer showing the frequency of the selected crop (Sugar beet) in the indicated area, with a color gradient from the lightest to the most saturated shade of the HEX b3573b color.
Description of how the function works

Data and classifier preparation:
Initializes the map for cords coordinates.
Loads a classification model based on optical and SAR data to include both current and historical image data.

Cultivation frequency classification:
Based on selected historical dates (2015-2023) and current data from 2024, the function iterates through Sentinel images and creates a sum of layers, which allows you to estimate the frequency of occurrence of the selected crop in a given area.
A classification is added to each layer according to the ID of the selected crop.

Result visualization:
The resulting map contains a result layer that shows the frequency of the selected crop.
Uses shades of color (provided by the user) to indicate different frequency levels.
A legend for the color gradient is generated on the map, making the data easier to interpret.


## crop_index function

The crop_index function generates a monoculture index map for the selected area, allowing you to assess whether fields are subject to crop rotation (crop rotation) or monoculture cultivation. The index ranges from 1 (no crop rotation, full monoculture) to 0.1 (full crop rotation, crops changed every year).

### Function arguments:

cords (required): Coordinates of the center of the area in the format [latitude, longitude].

color (required): HEX color, e.g. '445d07', used to visualize the index on the map. The function generates light shades of this color, creating a gradient to visualize different levels of the monoculture index.

start_date (optional): Start date of the Sentinel image for assessing the current crop status, default '2024-06-30'. It is recommended to leave this value unchanged.

stop_date (optional): End date of the Sentinel image, default '2024-07-01'.

cloudy_pixel_percentage (optional): Maximum percentage of clouds in the selected image. Defaults to 100, can be changed to reduce the amount of clouds in the image.

### Example of using the function

To generate a monoculture index map, make the following call:

```python
Map = crop_index([52.68, 18.28], '445d07')
```

The function returns a Map map with a monoculture index layer, which shows the monoculture index for a given area ranging from 1 (full monoculture) to 0.1 (full crop rotation).
Description of how the function works

Preparation of the map and classifier:
Initializes the map for cords coordinates.
It loads a classification model based on Sentinel optical data and then analyzes the current crop.

Creating a monoculture index:
The function processes historical data (from 2015 to 2023) to calculate crop variability over time.
It compares subsequent SAR images from selected years and determines crop variability based on the differences between subsequent classifications.
For each change in cultivation (i.e. switching to a different type of cultivation), the function increases the index by a specific value, which translates into a higher variability index (lower monoculture index).

Index visualization:
The map contains a layer with a color gradient corresponding to the monoculture index values, making monoculture and rotation fields easily distinguishable.
Creates a monoculture index legend that helps interpret index values ​​on the map - a value of 1 indicates full monoculture and 0.1 indicates full crop rotation.

The function allows you to quickly assess which fields are subject to crop rotation and which are monocultured, which is important for assessing biodiversity and soil health in a given region.
