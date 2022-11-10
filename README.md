## Introduction

There is a need to import into the system large raster files stored on the server's file system. To achieve this, the user will need to define the raster file details and mapping using a json file. A sample json file is as below:

```json
{
    "help": {
        "_comment_": "This is only a sample file showing how the contents should be structured",
        "defaults": "This key contains values that are repeated against rasters. If a specific raster has a separate value from the default, specify that value at the raster object level",
        "raster_category": "Must be one of ['NDVI', 'LULC', 'SOC', 'Rainfall', 'Aspect', 'Forest Loss', 'SAVI', 'MSAVI', 'Evapotranspiration', 'Ecological Units', 'Soil Slope', 'Soil Group', 'Soil Drainage', 'Soil Parent Material', 'Soil Texture', 'Soil Rock Fragments', 'Population Density', 'Land Use Density', 'Fire Risk', 'Erosion Protection', 'Drought Resistance', 'Plant Cover', 'Aridity Index', 'Tree Cover Loss', 'Forest Activity Map', 'Vegetation Cover', 'Soil Roughness', 'Soil Crust', 'Erodible Fraction', 'Climatic Erosivity', 'Rainfall Erosivity', 'Soil Erodibility', 'Slope Steepness', 'Cover Management', 'Conservation Practices', 'Geomorphology', 'Coastal Slope', 'Sea Level Change', 'Shoreline Erosion/Accretion', 'Tide Range', 'Wave Height']",
        "raster_source": "Must be one of ['LULC', 'Modis', 'Landsat 7', 'Landsat 8', 'Hansen', 'Sentinel 2']",
        "admin_zero_id": "Only set if the raster is for specific admin_level_zero. Valid values are: [1, 2, 3, 5, 6, 7, 9] for [Tunisia, Algeria, Libya, Morocco, Western Sahara, Mauritania, Egypt] respectively. This will change depending on the existing values. So pick the correct values from the existing data",
        "raster_file": "You must enter a fully qualified name e.g /home/nyaga/datasets/ndvi.tiff. For Docker setup, ensure the datasets are copied into the shared volumes else they will not be available within the container"
    },
    "defaults": {
        "resolution": 300
    },
    "rasters": [
        {
            "name": "Test name",
            "raster_year": 2019,
            "raster_category": "NDVI",
            "raster_source": "Modis",
            "file_path": "/home/sftdev/Pictures/sql.png"
        },
        {
            "name": "Test name 2",
            "raster_year": 2020,
            "raster_category": "LULC",
            "raster_source": "Modis",
            "file_path": "/home/sftdev/Pictures/home.png",
            "resolution": 10,
            "admin_zero_id": 2
        }
    ] 
}
```

- **help** key refers to the general guidelines to follow when defining the mappings
- **defaults** key contains values that are likely to be repeated for each raster. For example, if all the rasters had the same resolution or same raster_source, then it makes sense to define that value at the **defaults** section
- **rasters** key contains a list of the rasters to be imported. Possible properties are:

```python
 {
            "name": "Test name 2", # The name to be assigned to the raster
            "raster_year": 2020, # The year associated with the raster
            "raster_category": "LULC", # The raster category. Refer to the **help** section for possible values
            "raster_source": "Modis", # The raster source. Refer to the **help** section for possible values
            "file_path": "/home/sftdev/Pictures/home.png", # This is the absolute file path for the raster
            "resolution": 10, # The resolution of the raster
            "admin_zero_id": 2  # The country id associated with the raster. Refer to **help** section on how to define this value
        }
```
- **NB**. The script checks the existence of another raster by filtering using **raster_year, raster_category, raster_source and admin_zero**. If a raster exists, the raster is not re-imported

## How to use ?

- Update the json file with the mappings. Once you are done, login to Django Admin and navigate to the **Data Import Settings**. Attach the file and save

![image](/uploads/5e545b32e246161645dc156bbe8c0351/image.png)

![image](/uploads/e7d4600cda04c56924cd2ddb2cd52bd4/image.png)

- SSH into the server and run the following commands

```bash

cd oss-ldms
docker-compose -f docker-compose.prod.yml exec web python manage.py shell
from ldms.load_rasters import import_from_json
import_from_json()

```

![image](/uploads/ad50102852f8b0529cc8ce24902a0fd3/image.png)

- Sample output should be as below:

![image](/uploads/22a63f869d68a990ba0a92722f8a2c19/image.png)