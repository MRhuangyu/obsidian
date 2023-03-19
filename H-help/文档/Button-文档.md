
```button  
name Open Previous Daily Note  
type command  
action Periodic Notes: Open previous daily note  
```  
^button-previous

Turn spellcheck on/off:

```button  
name Toggle spellcheck  
type comand  
action Toggle spellcheck  
color blue  
```  
^button-spellcheck

### [](https://github.com/shabegom/buttons#link-button)Link Button

Open the Obsidian Forum:

```button  
name To the Forum Batman!  
type link  
action [https://forum.obsidian.md/](https://forum.obsidian.md/)  
```  
^button-forum

### [](https://github.com/shabegom/buttons#template--line-button)Template & Line Button

#### [](https://github.com/shabegom/buttons#append)Append

Append a Log Template Note:

```button  
name Log  
type append template  
action Hourly Log Template Note  
```  
^button-log

Append the current time:

```button  
name Log  
type append text  
action 21:47  
templater true  
```

#### [](https://github.com/shabegom/buttons#prepend-template)Prepend Template

Replace a Weather Template Note with the updated Weather:

```button  
name Current Weather  
type prepend template  
action Weather Template Note  
replace [1,5]  
```  
^button-weather

Prepend a weekly todo list and remove other buttons




Event better, setup those buttons and then add them all on one line as Inline Buttons:

`button-mon` `button-tues` `button-wed`

### [](https://github.com/shabegom/buttons#add-template-at-line)Add Template at Line

Say you want the weather to appear at a specific place in your note that isn't directly beside the button:

```button  
name Current Weather  
type line(1) template  
action Weather Template Note  
replace [1,5]  
```  
^button-weatherLine

#### [](https://github.com/shabegom/buttons#new-note-from-template)New Note From Template

Create a new note for an upcoming meeting based on a Meeting Note Template:

```button  
name New Meeting  
type note(Meeting, split) note  
action Meeting Note Template  
```  
^button-meeting

Dynamically add the hour and minute to the note title:

```button  
name New Meeting  
type note(Meeting-21-03, split) note  
action Meeting Note Template  
templater true ```  
^button-meeting2

### [](https://github.com/shabegom/buttons#calculate-button)Calculate Button

Do some simple math:

```button  
name Add Em Up  
type calculate  
action 2+2 ```  
^button-add

Reference numbers outside of the Button:

Bananas Have: 5  
Bananas Lost: 5

```button  
name How Many Bananas Today?  
type calculate  
action $267-$266  
color yellow  
```  
^button-bananas

Natural Language Math:

5 dogs plus 2 cats divided by 2 people

```button  
name Who Get The Pets?  
type calculate  
action $278 class sad-button  
```  
^button-breakup

The calculate button uses [math-expression-evaluator](https://github.com/bugwheels94/math-expression-evaluator), so should support any symbol supported by that library.

### [](https://github.com/shabegom/buttons#swap-buttons)Swap Buttons

Let's create a Swap Button using the button-block-id of previous Buttons:

```button  
name Crazy Swap Button swap [add,meeting,forum]  
```  
^button-swap

Then insert that button inline `button-swap`

1.  On the first click of Crazy Swap Button we will add 2+2
2.  On the second click of Crazy Swap Button we will create a new Meeting note
3.  On the third click of the Crazy Swap Button we will go to the Obsidian forum

Note: swap count is reset if you close the note.