# MetaData-Datacreation

## Task-1 ((Reading Image Metadata)
This task is simple and uses PIL (pillow) library. First step is mounting the google drive to the notebook where the dataset is present.
Next, step is to read the path of the image data files using os library of python that handles the path the train dataset.

['germany-english-garden_10.jpg',
 'taiwan-jiufen_15.jpg',
 'japan-katsura-river_24.jpg',
 'italy-garda-lake-sailing-club_18.jpg',
 'irland-dingle_9.jpg',
 'turkey-bodrum_5.jpg',
 'germany-allgaeu-fliegenpilz_4.jpg',
 'germany-garching-heide_7.jpg',
 'england-london-bridge_2.jpg',
 'DSCN0010_21.jpg']
 
PIL library provides a function named "getexif()" that provides the id's of all the Metadata tags and data that is present in the image.

  img = Image.open(os.path.join(files,images))
  meta_data_id = img.getexif()
  
By using a for loop, the image Metadata is mapped to the respective tags and stored in a dictionary in the given format {tag_name: data}.

    tag = TAGS.get(_id,_id)
    meta_data = meta_data_id.get(_id)

Important is to naote that the Metadata present in 'bytes' format needs to be decoded. To encounter this, try and except are used to ignore the values that are unable to decode and replace such values with an empty string.

````try:
      if isinstance(meta_data,bytes):
        meta_data = meta_data.decode()
    except:
      meta_data = "".
      
      
Task-1 ((Reading Image Metadata)
