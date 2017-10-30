# videofacesummary
makes a summary of faces seen in a video

# *Video Processing container*

## build the container

```Shell
docker build -f Dockerfile.process -t vfs.process .
```

## run the container to process videos with default parameters

```Shell
docker run -v /dirWith1movie:/in -v /outputDir:/out vfs.process
```

## run the container to process videos with custom parameters

```Shell
docker run -v /dirWith1movie:/in -v /outputDir:/out vfs.process directFeatures.py --reduceby 1.0 
            --every 30 --tolerance 0.50 --jitters 4
```

  * **reduceby** = Factor to reduce video resolution (ex: 1.0 = original resolution, 2.0 -> reduce horizontal and vertical resolution by 2)  
  * **every** = Process every nth frame (ex: 30 = every 30th frame of video)
  * **tolerance** = Different faces are tolerance apart (ex: 0.4->tight 0.6->loose)
  * **jitters** = How many perturberations to use when making face vector

## run the container to build a reference set of faces from processed videos
**Note: This will try to process all pickle files that were vectorized via resnet50 in the "/out" volume by default and build a reference set of faces at /reference/face_reference_set_resnet50.pkl and a hash table of filenames and content hashes at /reference/hash_table.pkl**

```Shell
docker run -v /dirWithDetectedFaces:/out -v /referenceDir:/reference vfs.process resolveVideos.py
```

## run the container to resolve processed video output from multiple videos with custom parameters
```Shell
docker run -v /dirWithDetectedFaces:/out -v /referenceDir:/reference vfs.process resolveVideos.py 
             --vectors_used openface --detected_faces_folder /out --reference_faces_file face_reference_set_openface.pkl 
             --hash_table_file anotherfile.pkl --tolerance 0.6  --verbose False
```

  * **vectors_used** = Vectorization technique used to generate pickle files (resnet50, facenet_tf, openface). Note that 
          vector distance comparison between different vectorization techniques does not work. (default = resnet50)  
  * **detected_faces_folder** = Folder containing detected faces pickles files (default:/out)  
  * **reference_faces_file** = Pickle file in '/reference' containing reference set of detected faces (default: face_reference_set_resnet50.pkl)
  * **hash_table_file** = Pickle file in '/reference' containing hash table of filenames and content hashes (default: hash_table.pkl)
  * **tolerance** = Different faces are tolerance apart (ex: 0.4->tight 0.6->loose)
  * **verbose** = Print out detailed information related to processing time and results (default: False)")

# *Jupyter container*
### Interactive playing with data processed by the *Video Processing container*

## build the container

```Shell
docker build -f Dockerfile.notebook -t vfs.notebook .
```

## run the container

```Shell
docker run -v /someDirWithdatafiles:/in -p8888:8888 vfs.notebook
```