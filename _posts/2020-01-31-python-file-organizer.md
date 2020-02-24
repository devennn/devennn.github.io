---
layout: post
published: false
title: Simple GUI based file organizer
---

Usually, at the end of semester, my Download folder is full of files. They are usually useful, but trying to organize them is a pain. Luckily, I know Python! Let's automate them. So, my plan was to build a semi-automatic file organizer.

How it works:
1) User choose directory to organize
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

