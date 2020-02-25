---
layout: post
published: false
title: Simple GUI based file organizer
---

Usually, at the end of semester, my Download folder is full of files. They are usually useful, but trying to organize them is a pain. Luckily, I know Python! Let's automate them. So, my plan was to build a semi-automatic file organizer.

How it works:
1) User choose directory to organize
2) User choose What to do - Reararnge or Rename
2) Program do the rest!

I think this would be useful not just for download folder, but also to other messy folder such as Desktop etc.

## Program Breakdown

I divide the program into 2 parts:
- GUI - Mainly for interaction. I do not want to open terminal just to organize files. So, having GUI make it easier and more intuitive
- Main Process - Where all the processing happens.

Step by step:
1) User select directory
2) User choose menu - Rearrange or Rename
3) User's choices are processed
4) Send Done! to indicate successful process

As stated in step 2, user is able to choose 2 process
- Reararnge: To group file according to file extension
- Rename: Renaming batch of files

## Program code

main driver function
```python
if __name__ == '__main__':
    values, process_chosen = run_gui()
    if(process_chosen != -1):
        err = access_dir(values, process_chosen)
        if(err != 0):
            sg.popup("Process error")
        else:
            sg.popup("Done All!")
```

For simplifying GU creation, I use PySimpleGUI. [Read official tutorial](https://pysimplegui.readthedocs.io/en/latest/tutorial/)

In gui.py
```python
def run_gui():
    sg.ChangeLookAndFeel('Reddit')
    layout = [
        [sg.Text('File Organizer', size=(35, 1), justification='center',
            font=("Arial", 20), relief=sg.RELIEF_GROOVE)],
        [sg.Text('_'  * 80)],
        [sg.Text('Choose Options', size=(20, 1), justification='left',
            font=("Arial", 15))],
        [sg.Checkbox('Rearrange', size=(10,1), key='_0_')],
        [sg.Checkbox('Rename', size=(10,1), key='_1_'),
            sg.InputText('New_Filename', key='_newName_')],
        [sg.Text('_'  * 80)],
        [sg.InputText('Choose A Folder', key='_folder_'), sg.FolderBrowse()],
        [sg.Submit(tooltip='Click to submit this window'), sg.Cancel()]
    ]
    path = Path('.').parent.absolute()
    path = os.path.join(path, 'dist', 'icon.ico')
    window = sg.Window(
        'File Organizer', layout, default_element_size=(40, 1),
        grab_anywhere=False, icon=path
    )
    event, values = window.read()
    chosen = check_options_chosen(values)
    if(chosen == -1):
        return values, chosen
    return values, chosen
    
def check_options_chosen(values):
  options_chosen = False
  if(values['_0_'] == True):
      return 0
  if(values['_1_'] == True):
      return 1
  if(options_chosen == False):
      return -1
```

Tweaking the layout function will change the appearance of GUI. ```sg.Window``` will create window based on layout settings. ```window.read()``` will read event that is happening such as clicks, box tick, text fill etc.

As seen from driver function, main, the gui should return process chosen and values for it.

## Main Processes

I set every option as a class so that it's easy for me to change/add features in the future.

### Arrange files:
```
class Arrange:

    def __init__(self, allFile=[], dirToAccess=""):
        logger.info('=== Initializing class: Arrange ===')
        self.allFile = allFile
        self.dirToAccess = dirToAccess

    # Create folder based on file extension and move all file into folder
    def process_file(self):
        logger.info('=== Start process_file ===')
        for file in self.allFile:
            dir = os.path.join(self.dirToAccess, file)
            filename, file_extension = os.path.splitext(dir)
            if(file_extension != ""):
                file_extension = file_extension.strip('.')
                new_fname = ("%s_file" %file_extension)
                new_fpath = os.path.join(self.dirToAccess, new_fname)
                try:
                    os.mkdir(new_fpath)
                except FileExistsError:
                    pass
                new_fpath = os.path.join(new_fpath, file)
                os.rename(dir, new_fpath)
```

This class perform file organizing according to extension. It started by stripping all files in current directory to obtain their extension. Then, new folder will be created such as ```file_extension_file``` . The program will the try to create new directory and will pass if the directory exists.

After ```os.mkdir``` the old directory will be rename to the newly made oe existing directory. Applying this process to every file will rearange all of them.

### Rename Files:

Before going to process class, it has to pass through error checking function. This is to make sure every new file name specified by user does not contain characters that cannot be used in Windows filename system.

```
# Check if string entered containing forbidden characters on windows
def check_rename_newname(str):
    logger.info('=== Start check_rename_newname ===')
    regex = re.compile('[<>:"/\|?*]')
    if(regex.search(str) == None):
        return 0
    else:
        return -1
```

Using regex search will return None if no position in the string matches specified pattern. I want the return value to be ```None``` which indicates no forbidden characters used. So, other than that, all values can be discarded.


```
class Rename:

    def __init__(self, allFile=[], dirToAccess=""):
        logger.info('=== Initializing class: Rename ===')
        self.allFile = allFile
        self.dirToAccess = dirToAccess

    # Renaming files
    def rename_files(self, new_name):
        logger.info('=== Start rename_files ===')
        i = 1;
        dir = self.dirToAccess + "/"
        for file in self.allFile:
            filename, file_extension = os.path.splitext(file)
            file = os.path.join(dir, file)
            new_path = dir + str('{}_{}'.format(new_name, i)) + file_extension
            os.rename(file, new_path)
            logger.info('=== os.rename: {} -> {}'.format(file, new_path))
            i += 1
```

Renaming process is almost the same as rearangging, except the function has argument ```new_name``` which is required. User can choose to fill up the GUI text box for renaming options. If not, default placeholder ```New_Filename``` will be used as the new name.

## Compilation

I want to create an executable file so I don't have to open terminal to use this program. To do so, I use pyinstaller package which is very handy.

> pyinstaller.exe --onefile --windowed --icon=dist/icon.ico gui.py -n Filorg

Explanation:
- --onefile : Since my entry point is gui.py, i only need to specify a file.
- --windowed : Making sure the program will not start console window when program is running
- --icon : Program icon
- -n : Executable file name. For this proram, i named it Filorg

When compilation is done, the executable file can be found in dist/ folder.

Click Filorg.exe:

![File Organizer](https://raw.githubusercontent.com/devennn/mysite/master/images/Filorg.PNG?token=ALZQJUU7IGNHBW6IXH6BDLC6KSJA4)



