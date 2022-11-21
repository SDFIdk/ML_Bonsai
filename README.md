# ML_Bonsai
Jupyter notebooks for processing a national land cover map of DK  

## Quick Guide to running befæstelsekortlægningen and producing a ML gennerated LC-map.

### Install Prerequisits
- A conda/mamba shell running a predefined 'job' enviroment
- A osgeo/gdal installation or QGIS installation with OSGeo4W shell
- A machine with GPU 1080 GTX or better

### Usage
1. Setting up enviroment
 	- Run a mamba prompt using the "job" enviroment. Navigate to folder containing jupyter notebooks (EX: E:\Bonsai2022FraAsk\scripts) and write [>>>jupyter notebook ] in prompt to start jupyter notebooks.

2. Running scripts
	- Start with the "bonsai_batcher" script. In code-block 4 set output directory (bdir) and input training data (training). In code-block 7 set input directory (inlist). Run all code-blocks in notebook. **This runs predictions and outputs classifications.**
	- Next run the "newMode" scipt. in code-block 7 set directory/dicectories of file/files you wish to run the mode-filer on (superDir). Run all code-blocks. A new set of folders containing filtered images should be created in the output directory. **This smoothes the data using a [mode filter](https://warwick.ac.uk/fac/sci/statistics/staff/research_students/ip/postphd/)**
	- Remove or save the non-mode blocks from the output directory or the "burner" script will burn values into the non-mode folder as well.
	- Lastly run the "burner" script. In codeblock 4 set the input directory (indir) and the directory of the vector layers you want burnt into the data (vecdir). Run the 5 code-blocks from the top. **This will 'burn' buildings, water, fields and coastline from geodanmark vector layers into dataset.**

3. A vrt and ovr file can be made and final result can be moved to a WMS server.
	- Build tif for each block using gdalbuildvrt and then gdal_translate (Ex `>>>gdal_translate -a_nodata 0 -co compress=LWZ -co tiled=YES src_data dst_data`)
	- Build a vrt of the 53 block tifs and then run a gdaladdo on that vrt (EX `>>>call gdaladdo sep2022.vrt -r mode -ro --config GDAL_CACHEMAX 32768 --config GDAL_NUM_THREADS ALL_CPUS --config INTERLEAVE_OVERVIEW PIXEL --config COMPRESS_OVERVIEW DEFLATE --config BIGTIFF_OVERVIEW YES 2 5 15 50 150 500 1500`)
	- Check that vrt files have relative, not full, paths to tiffs and that "relativeToVRT" is set to 1 (relativeToVRT="1")
	- Copy block tifs, national vrt and overview to server (Ex: kmsload157.kmsext.dk) and edit .map-file to point to national vrt file
	
## OBS! For the below! Remember to create new folders, lists and filenames, so you dont overwrite existing one that could be usefull to recreate older project runs. Or just for inspiration or comparisons.
	
- Every year, when new data (orthophotos & GeoDK vectorlayers) is available, GeoDK vectors and training data needs to be updated:
	- relevant vectorlayers (buildings, coast, fields, lakes, water) should be updated if changes have ocured. Get from GeoDK database or lbst homepage in regards to field data. Place in project (Bonsai2022FraAsk) "dk_vector" folder.
	- create new training data from new orthophotos:
		- In the project folder "scripts" there is a "findTrainingImage" jupyter notebook that allows you to get current images over certain choosen areas of interes and copy them from the NAS to your local project (train->trainimages). Run notebook to copy images, then make a vrt of these images.
		- In the folder "train" there is a geopackage file with at bunch (9643) of points randomly selected over certain areas of DK. Input file into QGIS along with the vrt-file made in the previous step.
		- In QGIS run the "udtræk rasterværdier til punkter" tool from the procesing toolbox. This adds the RGBI values to the geopackage point file.
		- copy these values to at txt file (EX: copy from QGIS-attribute-table to excel then copy only the "cid" and "RGBI" values to txt). This will now be the new training data. (EX: all_train_F19.txt for 2019 data)  
				

## Build
