# ML_Bonsai
mafal@sdfi.dk

Quick Guide to running befæstelsekortlægningen and producing a ML gennerated map.

1. run a mamba prompt using the "job" enviroment. Navigagte to folder containing jupyter notebooks (EX: E:\Bonsai2022FraAsk\scripts) and write [>>>jupyter notebook ] in promt to start jupyter notebooks.

2. Running scripts
	*start with the "bonsai_batcher" script. In code-block 4 set output directory (bdir) and input training data (training). In code-block 7 set input directory (inlist). Run all code-blocks in notebook.
	*next run the "newMode" scipt. in code-block 7 set directory og file you wish to run the mode-filer on (superDir). Run all code-blocks. A new set of folders containing filtered images should be created in the output directory.
	*remove or save the non-mode blocks from the output directory or the "burner" script will burn values into the non-mode folder as well.
	*lastly run the "burner" script. In codeblock 4 set the input directory (indir) and the directory of the vector layers you want burnt into the data (vecdir). Run the 5 code-blocks from the top.

3. Result can now be moved to WMS and a vrt and ovr file can be made.
	*build tif for each block using gdalbuildvrt and the gdal_translate (Ex >>>gdal_translate -a_nodata 0 -co compress=LWZ -co tiled=YES src_data dst_data)
	*build vrt of tifs for each block and then run gdaladdo on that vrt
	*Check that vrt files have relative, not full, paths to tiffs and that "relativeToVRT" is set to 1 (relativeToVRT="1")
	*copy block tifs, national vrt and overview to server (Ex: kmsload157.kmsext.sk)
	
OBS! For the below! Remember to create new folders, lists and filenames, so you dont overwrite existing one that could be usefull to recreate older project runs. Or just for inspiration or comparisons.
	
Every year, when new data (orthophotos & GeoDK vectorlayers) is available, GeoDK vectors and training data needs to be updated:
	* relevant vectorlayers (buildings, coast, fields, lakes, water) should be updated if changes have ocured. Get from GeoDK database or lbst homepage in regards to field data. Place in project (Bonsai2022FraAsk) "dk_vector" folder.
	* create new training data from new orthophotos:
		*In the project folder "scripts" there is a "findTrainingImage" jupyter notebook that allows you to get current images over certain choosen areas of interes and copy them from the NAS to your local project (train->trainimages). Run notebook to copy images, then make a vrt of these images.
		*In the folder "train" there is a geopackage file with at bunch (9643) of points randomly selected over certain areas of DK. Input file into QGIS along with the vrt-file made in the previous step.
		*In QGIS run the "udtræk rasterværdier til punkter" tool from the procesing toolbox. This adds the RGBI values to the geopackage point file.
		*copy these values to at txt file (EX: copy from QGIS-attribute-table to excel then copy only the "cid" and "RGBI" values to txt). This will be the new training data. (EX: all_train_F19.txt for 2019 data)
		
