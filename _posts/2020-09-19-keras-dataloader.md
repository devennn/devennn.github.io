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
