---
layout: post
title: Three types of Keras Data Loader to load image dataset
description: "Keras Data Loader for Image Dataset"
keywords: "Keras, Data Loader, Images"
published: False
---

One part of the development cycle that took lots of time is creating data loader. Almost every real world project require the developer to carefully plan this part. The bigger the dataset, the more effort need to be put in the planning. In this blog post, I want to present three methods of data loading that can be used for any Keras projects.

## Built-in Image Generator

This generator assume you to arrange your data in this format.

```

```

As you can see, the data are placed in their respective classes. Then you can load the data using keras built-in ImageDataGenerator feature.

```python
def get_generator(data_path, batch_size, image_size, label_type, training=True):

    if training:
        datagen = ImageDataGenerator(
            width_shift_range=0.1, height_shift_range=0.1,
            shear_range=0.2, zoom_range=0.2, horizontal_flip=True,
            rescale=1. / 255.0
        )
        shuffle=True
    else:
        datagen = ImageDataGenerator(
            rescale=1. / 255.0
        )
        shuffle=False

    data_flow = datagen.flow_from_directory(
        directory=data_path, batch_size=batch_size,
        target_size=image_size, class_mode=label_type,
        shuffle=shuffle
    )

    return data_flow
```

In the code above, the ```data_path``` is the directory containing all the classes. Usually, training and testing data required different preprocessing steps. By passing the required transformation options, your output images are transformed automatically. The ```flow_from_directory``` method will do the heavy work to pull out and apply preprocessing to your data. One thing to note is the ```class_mode```. You have to chosse the right class mode to ensure your data label is processed correctly. Refer to Keras documentation to see more control options.

## Custom Data Generator 1

Sometimes, you might need a lot more preprocessing steps that are not available in the built-in feature. This is when custom Data Loader is useful. Take example of the dataset below, 

```
data/test_set\cats\cat.4999.jpg	cats
data/test_set\cats\cat.5000.jpg	cats
data/test_set\dogs\dog.4001.jpg	dogs
data/test_set\dogs\dog.4002.jpg	dogs
```
The dataset are for cats vs dogs classifier. All the paths and labels are saved into train and test txt files which can be loaded into lists using the code below.

```python
with open("train_set.txt", "r") as f:
    data = f.readlines()

img_path_list, label_list = [], []
for d in data:
    img_path, label = d.strip().split('\t')
    img_path_list.append(img_path)
    label_list.append(label)
```

```img_path_list``` store all the path to images while ```label_list``` store their respective labels. Then your custom data loader will look something like this

```python
class Custom_ImageGenerator(Sequence):
    def __init__(self, data_filenames, labels, label_encode, batch_size, image_size):
        self.data_filenames = data_filenames
        self.labels = labels
        self.label_encode = label_encode
        self.batch_size = batch_size
        self.image_size = image_size
        self.work_dir = Path(".").parent.absolute()

    def __len__(self):
        return (np.ceil(len(self.data_filenames) / self.batch_size)).astype(np.int)

    def __getitem__(self, idx):
        data_batch = self.data_filenames[idx * self.batch_size : (idx+1) * self.batch_size]
        data_labels_batch = self.labels[idx * self.batch_size : (idx+1) * self.batch_size]

        temp = []
        for img_path in data_batch:
            img = cv2.imread(os.path.join(self.work_dir, img_path))
            img = cv2.resize(img, self.image_size)
            img = img / 255.
            temp.append(img)
        
        encoded_label = [self.label_encode[label] for label in data_labels_batch]

        processed_img_batch = np.array(temp)
        encoded_label_batch = np.array(to_categorical(encoded_label))

        return processed_img_batch, encoded_label_batch
```

The whole process of loading and processing the data will be...

```python
if __name__ == "__main__":

    # create_files()

    label_encode = {"cats" : 0, "dogs" : 1}

    with open("train_set.txt", "r") as f:
        data = f.readlines()
    
    img_path_list, label_list = [], []
    for d in data:
        img_path, label = d.strip().split('\t')
        img_path_list.append(img_path)
        label_list.append(label)

    train_gen = Custom_ImageGenerator(img_path_list, label_list, label_encode, batch_size=64, 
                                        image_size=(224, 224))
```

To understand how the code works, you need to know few things. The main ingredients in out custom made class is ```Sequence```. This is a built-in Keras features that not only used in their built in data generator but also optimized for multiprocessing. According to their official documentation, _This structure guarantees that the network will only train once on each sample per epoch which is not the case with generators_. 

To use ```Sequence```, you must implement two methods, ```__getitem__``` and ```__len__```. 
- ```__getitem__``` : This method will output data for the the batch specified by the argument ```index```
- ```__len__``` : This method computes the number of batch for every sequence. 

The only thing you care is what should you do in the ```__getitem__``` section which consists of how can you get the data for a given batch_size and idx. In this example, I store the imgs_path and labels as list which can be sliced accordingly.
