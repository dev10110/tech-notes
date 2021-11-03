---
layout: default
title:  Making a Manim Slide Deck
date:   2021-11-03
parent: Coding
---

# Making a Manim Slide Deck

Its really easy to make a full powerpoint slide deck that is animated using Manim. 


### Setup
Install the community edition of Manim [using these instructions](https://docs.manim.community/en/stable/installation.html) 
The following are commands for MacOS:
```
brew install py3cairo ffmpeg
brew install --cask mactex
pip3 install manim
```

Install the package that allows us [to make pptx out of manim slides](https://pypi.org/project/manim-pptx/) 
```
python3 -m pip install manim-pptx
```

Now you should be set to go. 

### Coding

Lets make an example of three slides:
-  A title slide
-  A table of contents
-  A square to circle animation

Normally this code looks like this:

```python
from manim import *
from manim_pptx import *

class TitleSlide(PPTXScene):
  def construct(self):

      main_title = Tex(r"Cool Anims")
      main_title.shift(0.5*UP)
      authors = Tex(r"Devansh Agrawal", font_size=36)
      authors.shift(1.5*DOWN)

      title_short = Title(r"Demos")

      self.play(
            Write(main_title),
            FadeIn(authors)
      )
      self.endSlide()
    
      self.play(
        Transform(main_title, title_short),
        FadeOut(authors),
      )
      self.endSlide()

class TOC(PPTXScene):
    def construct(self):
        title_short = Title(r"Demos")
        self.add(title_short)

        blist = BulletedList("Title", "Table of Contents", "Simple Animation")

        self.play(FadeIn(blist))
        self.endSlide()

class SquareToCircle(PPTXScene):
    def construct(self):
        circle = Circle()
        square = Square()
        square.flip(RIGHT)
        square.rotate(-3 * TAU / 8)
        circle.set_fill(PINK, opacity=0.5)

        self.play(Create(square))
        self.endSlide()
        self.play(Transform(square, circle))
        self.endSlide()
        self.play(FadeOut(square))
        self.endSlide()
```

and (assuming it was saved to a file called `slides.py`) you would compile and generate the videos and powerpoint by doing
```
manim -pqh slides.py -a
```
where the -p means you want a preview, -qh means high quality and -a means to run this for all the scenes.

The output of this, unfortunately, is three separate pptx files, which you will have to manually combine together. 


## INSTEAD,
Instead, we can ask manim to combine everything together for us in one shot:

We add the following **to the end** of `slides.py`:

```python

# define the set of slides you want
slides = [
    TitleSlide,
    TOC,
    SquareToCircle
]


class Slides(*slides):

    def setup(self):
        # setup each scene
        for s in slides:
            s.setup(self)

    def construct(self):
        # play each scene
        for s in slides:
            s.construct(self)
            # if there are any objects left at the end of the animation, remove them!
            if len(self.mobjects) >= 1:
                self.remove(*self.mobjects)
```

which essentially goes through, sets up each animation, and then constructs them in the sequence you have defined. 
It creates a single scene effectively, which means you get a single pptx file out!

To run this, simply run

```
manim -pqh slides.py Slides
```
which will generate a single final ppt file called `Slides.pptx`



Extra tip: If you want to add slide numbers, this might be a good spot!



