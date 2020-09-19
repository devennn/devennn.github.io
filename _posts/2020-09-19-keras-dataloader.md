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
└───data
    ├───test_set
    │   ├───cats
    │   └───dogs
    └───training_set
        ├───cats
        └───dogs
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

To understand how the code works, you need to know few things. The main ingredients in our custom made class is ```Sequence```. This is a built-in Keras features that not only used in their built in data generator but also optimized for multiprocessing. According to their official documentation, _This structure guarantees that the network will only train once on each sample per epoch which is not the case with generators_. 

By inheritting ```Sequence```, you must implement two methods, ```__getitem__``` and ```__len__```. 
- ```__getitem__``` : This method will output data for the the batch specified by the argument ```index```
- ```__len__``` : This method computes the number of batch for every sequence. 

The only thing you care is what should you do in the ```__getitem__``` section which consists of how can you get the data for a given batch_size and idx. In this example, I store the imgs_path and labels as list which can be sliced accordingly. You can apply any image preprocessing in this part.

## Custom Data Generator 2

In some projects, even custom Sequence data is not useful enough. I was working with OCR project where the train model has different train and test model input shape. One way to use the custom generator before is to change the change the model, having the same train and test input shape.

But, since most(almost all) of open source research won't do it this way, it is wise to not reinvent the process so you can have more references. And, using the built in sequence model is denfinitely not usable if you are loading from raw data as the labels are preprocessed differently.

For this, we are going to change the first custom data loader a little bit. The data are stored in the same manner as before which consists of image paths and labels. So, the first step is to load the the txt file to get the information. Then here comes the changes.

```python
class DataLoader(Sequence):

    __metaclass__ = abc.ABCMeta

    def __init__(self, filenames, labels, char_list, batch_size, training, image_size=(32, 128)):
        self.filenames = filenames
        self.labels = labels
        self.char_list = char_list
        self.batch_size = batch_size
        self.training = training
        self.w = image_size[0]
        self.h = image_size[1]
        self.max_label_len = 0

    def __len__(self):
        return (np.ceil(len(self.filenames) / self.batch_size)).astype(np.int)

    def __getitem__(self, idx):
        data_batch = self.filenames[idx * self.batch_size: (idx + 1) * self.batch_size]
        label_batch = self.labels[idx * self.batch_size: (idx + 1) * self.batch_size]

        X, y = self._generate_data(data_batch, label_batch)

        return X, y

    def _preprocess_image(self, img, w, h):
        if w < self.w:
            add_zeros = np.ones((self.w - w, h)) * 255.0
            img = np.concatenate((img, add_zeros))

        if h < self.h:
            add_zeros = np.ones((self.w, self.h - h)) * 255.0
            img = np.concatenate((img, add_zeros), axis=1)

        img = np.expand_dims(img, axis=2)

        # Normalize Images
        img = img / 255.0

        return img

    def encode_to_labels(self, txt, char_list):
        # encoding each output word into digits
        dig_lst = []
        for _, char in enumerate(txt):
            try:
                dig_lst.append(char_list.index(char))
            except:
                print(char)

        return dig_lst
    
    @abc.abstractmethod
    def _generate_data(self, data_batch, label_batch):
        # Method to generate batches of data
        pass
```

Our data loader still inherit from ```Sequence```. The addition is we also use abc library as the ```__metaclass__```. This library, through ``` @abc.abstractmethod``` creates abstraction of the decorated methods. In our example, we decorated ```_generate_data``` as abstract to be use somwhere else.

The somewhere else are...

```python
class TrainGenerator(DataLoader):

    def _generate_data(self, data_batch, label_batch):
        batch_imgs, batch_labels, input_len, label_len = [], [], [], []
        for img_path, label in zip(data_batch, label_batch):
            img = cv2.imread(img_path, cv2.IMREAD_GRAYSCALE)

            # Preprocess image
            w, h = img.shape
            if h > self.h or w > self.w:
                continue

            img = self._preprocess_image(img, w, h)
            label = self.encode_to_labels(label, self.char_list)

            batch_imgs.append(img)
            batch_labels.append(label)
            input_len.append(self.w - 1)
            label_len.append(len(label))

        batch_labels = pad_sequences(batch_labels, maxlen=self.max_label_len, padding='post', value=len(self.char_list))

        batch_imgs = np.array(batch_imgs).astype('float32')
        batch_labels = np.array(batch_labels).astype('float32')
        inputs_len = np.array(input_len).astype('float32')
        labels_len = np.array(label_len).astype('float32')

        return [batch_imgs, batch_labels, inputs_len, labels_len], batch_labels


class ValidationGenerator(DataLoader):

    def _generate_data(self, data_batch, label_batch):
        batch_imgs, batch_labels, input_len, label_len = [], [], [], []
        for img_path, label in zip(data_batch, label_batch):
            img = cv2.imread(img_path, cv2.IMREAD_GRAYSCALE)

            # Preprocess image
            w, h = img.shape
            if h > self.h or w > self.w:
                continue

            img = self._preprocess_image(img, w, h)
            label = self.encode_to_labels(label, self.char_list)

            batch_imgs.append(img)
            batch_labels.append(label)

        batch_labels = pad_sequences(batch_labels, maxlen=self.max_label_len, padding='post', value=len(self.char_list))

        batch_imgs = np.array(batch_imgs).astype('float32')

        return batch_imgs, batch_labels
```

Just by observing the output, you can understand that they need to be processed separately. Both classes inherit from the custom data loader whcih enables them to use all the methods and modify ```_generate_data``` separately.

You only need to call ```TrainGenerator``` and ```ValidationGenerator``` in the main function to load the data. These data loading technique not only limited to images, but as you can see, texts can be preprocessed the same way. It's very useful!

That's it for this post. Hope you will learn something useful. Thanks
