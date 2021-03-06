# MetaData-Datacreation

## Task-1 (Reading Image Metadata)
=====================

#### Libraries -\> os, PIL(Images, ExifTags,Tags), google.drive.colab

This task is simple and uses PIL (pillow) library. First step is
mounting the google drive to the notebook where the dataset is present.
Next, step is to read the path of the image data files using os library
of python that handles the path the train dataset.

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

PIL library provides a function named "getexif()" that provides the id's
of all the Metadata tags and data that is present in the image.

    img = Image.open(os.path.join(files,images))
    meta_data_id = img.getexif()

By using a for loop, the image Metadata is mapped to the respective tags
and stored in a dictionary in the given format {tag\_name: data}.

    tag = TAGS.get(_id,_id)
    meta_data = meta_data_id.get(_id)

Important is to naote that the Metadata present in 'bytes' format needs
to be decoded. To encounter this, try and except are used to ignore the
values that are unable to decode and replace such values with an empty
string.

    try:
       if isinstance(meta_data,bytes):
         meta_data = meta_data.decode()
    except:
       meta_data = ""


## Task-2 (Generate Excel Sheet)
=====================

#### Libraries -\> os, pandas, base64, HTML, PIL(Image), google.drive.colab, string

First, a dictionary is formed with teh format {imagename : id} using the
below function ehcih is self explanatory.

    id_image = {}

    for image in os.listdir(files):

      id = image.split("_")[-1].split(".")[0]
      id_image[image] = int(id)
      

So, using the basic ".spit()" function of string split the image file
name about '\_' and then splitting the string about '.'. This is
explained below

    name = ['germany-english-garden_10.jpg']
    name = ['germany-english-garden','10.jpeg']       (after splitting about "_" and taking string at index 1)
    name = ['10','jpeg']                              (after splitting about "." and taking string at index 0)

Second, a Pandas Dataframe is initialized with all 0(zero) values,index
range(0-24), columns=( Imagename , Resized Image, Rotated Image,
MetadataInfo). The index is provided on the bases of the id given in the
image file name as imagename\_id.jpeg.

    df = pd.DataFrame(0,index=np.arange(0,max(id_image.values())+1),columns=['Image_name','Resized Image','Rotated Image','MetadataInfo'])
    df.style.set_properties(subset=["Image_name"], **{'text-align': 'center'})
    df.head()

As, provided the names and metadata associated with the image file is
inserted into the dataframe at their respective id. The metadata
dictionary which is formed in the task-1 is used for metadata insertion
in the dataframe. The values (tag : metadata) of respective keys are
concatenated into a single string with a line break using "\n".join().

    for image, id in id_image.items():
      df['Image_name'][id] = image

    meta_tokens = {}

    for key, value in data.items():
      ls = data[key]
      temp = []

      for tag in ls:
        temp.append(str(tag[0])+": "+str(tag[1]))

      meta_tokens[key] = temp


    metadatainfo = {}
    for keys,values in meta_tokens.items():
      metadatainfo[keys] = "\n".join(values)
      
    for keys, values in metadatainfo.items():
      df['MetadataInfo'][df['Image_name'] == keys] = values

Now he important part, rendering or inserting the image into pandas
dataframe. This is done using HTML "<img src="">" tag.

The below functions return the thumbail of the image present at the
provided location. The image is resized into (512x512) and returned as
the thumbnail. The if statement hanndles the indices that are not
defined ins the dataset of the image id.

        def get_thumbnail_unrotated(path):
            if path == "":
              return ""

            i = Image.open(path)
            i = i.resize(size=(512,512))
            i.thumbnail((512, 512), Image.LANCZOS)
            return i
            

This function is same as above, just modified as it rotates the image 90
degree in clockwise direction.

        def get_thumbnail_rotated(path):
            if path == "":
              return ""
              
            i = Image.open(path)
            i = i.resize(size=(512,512))
            i = i.rotate(angle=-90)
            i.thumbnail((512, 512), Image.LANCZOS)
            return i

Thes functions are the HTML formatters that return the tag
"<img src="">" with image path as the src location.

        def image_formatter_unrotated(im):
            if im == "":
              return 0
            return f'<img src="data:image/jpeg;base64,{image_base64_unrotated(im)}">'
        
        def image_formatter_rotated(im):
            if im == "":
              return 0
            return f'<img src="data:image/jpeg;base64,{image_base64_rotate(im)}">'

These functions check for the path, if its string then they convert the
path into a thumbnail, otherwise it writes data in memory buffer and
decodes the buffer using base64encode (utf-8)

        def image_base64_unrotated(im):
            if isinstance(im, str):
                im = get_thumbnail_unrotated(im)
            with BytesIO() as buffer:
                im.save(buffer, 'jpeg')
                return base64.b64encode(buffer.getvalue()).decode()
                
        def image_base64_rotate(im):
            if isinstance(im, str):
                im = get_thumbnail_rotated(im)
            with BytesIO() as buffer:
                im.save(buffer, 'jpeg')
                return base64.b64encode(buffer.getvalue()).decode()

A new column a inserted into the dataframe that contains the image file
path.

        # Saving the file path of the images
        df['file_1'] = df.Image_name.map(lambda id: contradiction(id))

        # converting the path of the image to the HTML tag for rotated and 
        # unrotated image
        df['Resized Image'] = df.file_1.map(lambda f: get_thumbnail_unrotated(f))
        df['Rotated Image'] = df.file_1.map(lambda f: get_thumbnail_rotated(f))
        df.head()
        

Now, the dataframe is completed using following function that converts
the "<img src="">" tag to the image.

        HTML(df[["Image_name","Resized Image","Rotated Image",'MetadataInfo']].to_html(formatters={'Resized Image': image_formatter_unrotated, 'Rotated Image':image_formatter_rotated}, escape=False))
        
        ![image](https://user-images.githubusercontent.com/55994140/126458187-b9c0eca0-55fd-4e18-8afd-dcdcdd6befec.png)
