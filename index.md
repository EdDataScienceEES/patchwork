# Composing panels with Patchwork
#### 03/12/2021 
#### by Ailsa Staderlee: s1739682@sms.ed.ac.uk

## Tutorial Aims
This tutorial will introduce you to panelling graphs in the updated ggplot2 (version 3.3.5) using the patchwork package. This panelling will allow you to present your plots however you like along with other additions like annotation.
----

# Contents
#### 1. [Introduction](#intro)
#### 2. [Basic Syntax](#bs)
#### 3. [Labeling](#l)
#### 4. [More Advanced Layout](#mal)
#### 5. [Beautifying](#b)
#### 6. [Challenges](#c)


# 1. Introduction <a name="intro"></a>
One of R's best features is the graphs and figures it can allow you to make. Because of this, many of its updates are focussed around this goal. 
With the (at time of writing) most recent update to the ggplot2 package- a shining example of R's predisposition towards appealing figures- has made it no longer able to do panelling 
found in the [Data visualisation 2](https://ourcodingclub.github.io/tutorials/data-vis-2/) coding club tutorial.

This tutorial assumes at least partial familiarity with RStudio and ggplot2. If you are concerned that this tutorial isn't at the right level for you, 
a guide to Coding Club's tutorials can be found [here](https://ourcodingclub.github.io/tutorials.html). 
    
## Installing and Downloading the Packages
Again, this tutorial assumes a familiarity with some tidyverse packages, so code for their installation is not included.
If they are not installed, simply replace the "patchwork" in the install.packages() function with either of the packages.
You can check to see which packages have been installed or downloaded in the 'Packages' tab of RStudio's bottom right hand panel.

```
#Libraries ----
library(dplyr) #for data manipulation
library(ggplot2) #for data visualization
#install.packages("patchwork") #run if patchwork is not already installed
library(patchwork) #for plot arranging
```

## Downloading the data for the tutorial
This tutorial's data will be sourced from the [Palmer Penguins](https://allisonhorst.github.io/palmerpenguins/), whoes data can be installed directly into RStudio.
This dataset will also show up in the 'Packages' tab if you're unsure if you've already got it installed.

```
# Data----
#install.packages("palmerpenguins")
library(palmerpenguins)

#Explore data----
#We will be using the 'penguins' dataset within this package

str(penguins)
View(penguins)
```

# 2. Basic Syntax <a name="bs"></a>

### Constructing Boxplots for this section

The first thing we need to panel plots is some plots to panel!

```
#### Basic Syntax ####
#Boxplots----
#a boxplot of the Adelie species' body mass across each of the 3 islands
(bm_is<-ggplot(subset(penguins,penguins$species=="Adelie"),aes(x=island,y=body_mass_g))+
   geom_boxplot()+
   xlab("Island")+
   ylab("Body Mass (g)")+
   labs(title="Adelie Body Mass on Each Island"))
 
# a comparative boxplot of the body mass of the penguin species in on the Dream island (Adelie and Chinstrap)
(bmsp_dream<-ggplot(subset(penguins,penguins$island=="Dream"),aes(x=species,y=body_mass_g))+
    geom_boxplot()+
    xlab("Species")+
    ylab("Body Mass (g)")+
    labs(title="Comparing Body Mass of Adelie and Chinstrap Penguins", subtitle = "from Dream Island"))
  
# a comparative boxplot of the body mass of the penguin species in on the Biscoe island (Adelie and Gentoo)  
(bmsp_biscoe<-ggplot(subset(penguins,penguins$island=="Biscoe"),aes(x=species,y=body_mass_g))+
    geom_boxplot()+
    xlab("Species")+
    ylab("Body Mass(g)")+
    labs(title="Comparing Body Mass of Adeline and Gentoo Penguins", subtitle = "from Biscoe Island"))
```
These plots are all similar to eachother, making them easier to panel together without risking making a confusing mess
## Core punctuation

The basis of this package is in 3 punctuation marks: `+, / and |`. These commands tell the package how you would like to put the component plots together.

### +

`+` is the default. It prompts the patchwork package to put the plots together in the most linear or grid-like way it can, depending on how many plots there are.
It also treats all the plots as if they have the same significance, dividing the panel space taken up evenly between them.
```
#Core punctuation----
bm_is+bmsp_dream+bmsp_biscoe #Default layout
```

>pl1_1
<img width="720" alt="pl1_1" src="https://user-images.githubusercontent.com/91271747/144591149-05e34ba8-d90b-4065-9836-7ebcbf35ffa9.png">


Remember to use the zoom button when making composite graphs as the plots panel on RStudio may squish your graphs.
Checking zoom will help you judge how well you're actually laying out the plots

### /

`/` tells the package specifically to stack the plots on top of eachother:

```
bm_is/bmsp_dream/bmsp_biscoe #Verticle stacking

bm_is/bmsp_dream+bmsp_biscoe #here we see to '+' stacking instead of placing side by side
```

Both of these lines produce the same panel.This demonstrates the way that the default layout asked for by `+` adapts to the other layout decisions made in the line of code.

> pl1_2
<img width="720" alt="pl1_2" src="https://user-images.githubusercontent.com/91271747/144591263-86568b76-3f0d-4939-ab3d-24e1c4baaefc.png">

### |
`|` (which can be typed by 'Shift+\' on many laptops) tells R to line the plots up horizontally. The code below produces the same output as the first 'Default Layout' Code above.
```
bm_is|bmsp_dream|bmsp_biscoe #Horizonal line-up
```

## Nesting
Nesting is a way of prioritising commands by creating objects within the composing code
(it's alright if that sentence didn't make too much sense, these examples should make it clearer).
Nesting is most simply done with `()` and `-`.  
```
#Basic syntax: Nesting----
#Creating objects:

#adding brackets to our first panel demostrates how the 'objects' are created

bm_is+(bmsp_dream+bmsp_biscoe) #nesting bmsp_dream and bmsp_biscoe
```
This code first panels bmsp_dream and bmsp_biscoe (prioritising the command from the '+' between them), so before bm_is is introduced to the composition, the other two plots become an object.
The '+' command splits the available space evenly between each object. 
Therefore, where the unbracketted line of code was splitting the space between 3 objects (bm_is, bmsp_dream and bmsp_biscoe), the bracketed line is splitting the space between only two objects

>pl1_3
<img width="720" alt="PL1_3" src="https://user-images.githubusercontent.com/91271747/144591366-7954d93d-4268-4f37-ac4c-ac5b23ecd4ef.png">

We can see this clearer in an alternative way to code this panel:
 ```
 p1<-bmsp_dream+bmsp_biscoe #create object
bm_is+p1 #panel the 2 objects together
```
### -
'-' acts like a way to make an object everything to the left of it.
This object is then treated with the same level of priority as the object to the right of the hyphen.
(it is good practice to think of the '-' as a hyphen in this context rather than a minus as its usage is in no way an inverse of the '+')
```
bm_is+bmsp_dream-bmsp_biscoe
#note how composition space is split 1:1:2
```
<img width="720" alt="pl1_4" src="https://user-images.githubusercontent.com/91271747/144591566-fa49cd5c-45cb-4ea5-9113-a1fb7b430691.png">


:penguin: :penguin: :penguin: :penguin: :penguin::penguin: :penguin: :penguin: :penguin: :penguin::penguin: :penguin: :penguin: :penguin: :penguin:

Try messing around with creating different panelling with this punctuation to get more comfortable with it.
There are lots of different ways to get to each layout so give it a shot, the worst thing that could go wrong is that you get an error message.

Personally, I think that the best way to lay out these plots is with:
```
bm_is|bmsp_dream/bmsp_biscoe
```
since bm_is and the other 2 plots are showing different types of comparisons


# 3. Labeling <a name="l"></a>
This section will mostly use plot_annotate().
Feel free to take a closer look at it with `??patchwork::plot_annotate()`
### Constructing scatterplots

Let's treat ourselves to some new plots 
```
#### Labeling ####
#Scatterplots----
#Scatterplot with trendline of the relationship between body mass and flipper length by species
(flbm<-ggplot(penguins,aes(y=body_mass_g,x=flipper_length_mm,colour=species))+
   geom_point()+
   labs(colour="Species")+
   ylab("Body Mass (g)")+
   xlab("Flipper Length (mm)")+
   geom_smooth(method="lm"))

#Remove NA data from the Sex column so the plots can model the data for each sex of the 3 species
penguins<-subset(penguins, !is.na(sex))

(flbm_a<-ggplot(subset(penguins, penguins$species=="Adelie"),aes(y=body_mass_g,x=flipper_length_mm,colour=sex))+
  geom_point()+
   geom_smooth(method="lm")+
  labs(subtitle = "Adelie", colour="Sex")+
  ylab("Body Mass (g)")+
  xlab("Flipper Length (mm)"))
 
(flbm_c<-ggplot(subset(penguins, penguins$species=="Chinstrap"),aes(y=body_mass_g,x=flipper_length_mm,colour=sex))+
  geom_point()+
  geom_smooth(method="lm")+
  labs(subtitle = "Chinstrap", colour="Sex")+
    ylab("Body Mass (g)")+
    xlab("Flipper Length (mm)"))

(flbm_g<-ggplot(subset(penguins, penguins$species=="Gentoo"),aes(y=body_mass_g,x=flipper_length_mm,colour=sex))+
  geom_point()+
  geom_smooth(method="lm")+
  labs(subtitle="Gentoo", colour="Sex")+
  ylab("Body Mass (g)")+
  xlab("Flipper Length (mm)"))
  ```
Now let's lay out the graphs:
```
#First lets lay out the graphs

flbm/(flbm_a+flbm_c+flbm_g)
```

> pl2_1
 <img width="720" alt="pl2_1" src="https://user-images.githubusercontent.com/91271747/144591658-bd206508-057c-4b78-a61f-e82d1b43904d.png">


## Titles
Through ggplot, each component plot can be titled and subtitled, but panels can also be standalone figures and therefore can need annotating with titles, subtitles and captions too.
```
#Labeling: Titles, subheadings and captions----


flbm/(flbm_a+flbm_c+flbm_g)+plot_annotation(title="The Relationship between Flipper Size and Body Mass for each Penguin Species")

#remember to check your zoom pop up if the plot panel shows your annotations being cut off

#We can also add a subheading
flbm/(flbm_a+flbm_c+flbm_g)+
  plot_annotation(title="The Relationship between Flipper Size and Body Mass for each Penguin Species",
                  subtitle = "the variation in this relationship between the sexes is also shown")

#we can use this function for a caption as well
flbm/(flbm_a+flbm_c+flbm_g)+
  plot_annotation(title="The Relationship between Flipper Size and Body Mass for each Penguin Species",
                  subtitle = "the variation in this relationship between the sexes is also shown",
                  caption = "Data from islands near the Palmer Station, Antarctica")
  ```
  
 > pl2_2
 <img width="720" alt="pl2_2" src="https://user-images.githubusercontent.com/91271747/144591725-dfcc7f08-24b0-4480-8dc7-f3dfde837b5c.png">

## Individual labels
Individually labeling the components of a panel is an easy way to give a shorthand name to them. This is a great help when writing up analysis of the plots.
```
#We can also 'tag' the individual plots in the panel
#use ?? to browse what options you have for labeling each panel (numbering, lettering, roman numerals etc.)
#??patchwork::plot_annotation

flbm/(flbm_a+flbm_c+flbm_g)+plot_annotation(tag_levels = '1')

#we can also add a prefix and a suffix to these tags

flbm/(flbm_a+flbm_c+flbm_g)+plot_annotation(tag_levels = '1', tag_prefix='Fig. ', tag_suffix=':')
```

> pl2_3
<img width="720" alt="pl2_3" src="https://user-images.githubusercontent.com/91271747/144591793-ded02141-3440-498e-ab3b-1469e044b9a4.png">
                                                                                                              
# 4. More Advanced Layout <a name="mal"></a>
Now that we can sort and label the component plots, it's time to introduce some handy additions
##  plot_layout() function
```
#### More Advanced Layout (MAL) ####
#The layouts from the 'Basic Syntax' of this tutorial can also be achieved using plot_layout()
#Using this function, we can specify the number of rows and columns we want the panel to be composed of

flbm+(flbm_a+flbm_c+flbm_g)+plot_layout(nrow=2)
```
"ncol=" in the same format will allow you to alter the number of columns the objects can use.

Still, "+plot_layout(nrow=2)" is a pretty longwinded replacement for "/" so why use it?
Maybe we decide for this panel that the total species plot at the top is taking up too much space.
In that case, we can use plot_layout to alter the size
```
flbm+(flbm_a+flbm_c+flbm_g)+plot_layout(nrow=2, heights = c(2:3))
```

> pl3_1
<img width="720" alt="pl3_1" src="https://user-images.githubusercontent.com/91271747/144591848-2d29f7c8-a1df-4fcb-973d-70113e488cec.png">

This lets us alter the ratio of the total available height of the space goes two which objects.
The same principle can be applied with "widths="

Clearly, nesting is a vital part of this process as it is each individual object along the width or height that is told- in order of top to bottom or left to right- what amount of the space it will need.

:penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin:

Ratios will continue to be an important part of using patchwork, so if you're unsure about them there's a [BBC Bitesize introduction to them](https://www.bbc.co.uk/bitesize/topics/zsq7hyc/articles/z8kfnbk)

## Legend wrangling
(don't worry, it doesn't take a Herculean effort)

You can mess around with sizes all you like, but sometimes there is just too much unnecessary stuff on your panel!
Luckily, we have some options for getting rid of unnecessary components like repeating legends.
```
#MAL:legends----

flbm/(flbm_g+flbm_c+flbm_a)+plot_annotation(tag_levels = '1', 
                                            tag_prefix='Fig. ', 
                                            tag_suffix=':')+
  plot_layout(guides="collect") # deleting duplicates of the legend for plots 2-4
```

>pl3_2
<img width="720" alt="pl3_2" src="https://user-images.githubusercontent.com/91271747/144591916-78c67bef-fa02-4151-a1f7-5ce1727ef9e4.png">

## Spacing
If the panel still looks crowded, we can add space in.

```
flbm/plot_spacer()/(flbm_g+flbm_c+flbm_a)+
  plot_layout(guides="collect")
  ```
  
 > pl3_3
 <img width="720" alt="pl3_3-1" src="https://user-images.githubusercontent.com/91271747/144591975-7a44cb1c-c3cd-41cc-aad1-7fd6c6664d39.png">

plot_spacer() allows you to create what is essentially a blank object. Each occurrence of it in the line of code will react in size and placement in the same way a plot would.
```
(flbm_g|plot_spacer()|flbm_a)/(plot_spacer()|flbm_c|plot_spacer())+
  plot_layout(guides="collect") #The blank spaces fit like the plots around them to create a checkered layout
  ```
Of course, with all this lovely space comes the impulse to cram it full of as much information as possible
(at least for me, you might have more restraint).

(Un)luckily, there is a function to indulge that want as well!

## Adding text
```
flbm/grid::textGrob('The figure above shows us that the individual species have a very similar relationship 
                     between Flipper Length and Body Mass.
                    The figures below show that there is more variation in this relationship between the sexes, 
                    though the extent of this variation differes from species to species')/(flbm_g+flbm_c+flbm_a)+
  plot_layout(guides="collect")
  ```
  
> pl3_4
<img width="720" alt="pl3_4" src="https://user-images.githubusercontent.com/91271747/144592037-9eff9b67-3c42-44e1-8b6c-186a88860398.png">

:penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin:

__Hints__

_1_
When adding text to the left of a plot, putting the command inside the wrap_elements() function is necessary for it to run. eg:
```
(wrap_elements(grid::textGrob('
Text on left side 
of a graph
needs an additional 
function')) + bm_is)/
  (bmsp_dream|grid::textGrob('
Text between
two graphs
does not need 
this function')|bmsp_biscoe)
```

_2_
It is easy when writing these sections to format them in a way that looks like bizarre poetry when plotted.
Make sure to only take a new line in the code when you want there to be a new line in the text on the output and, when in doubt, shove the text to the left side of the script panel 

# 5. Beautifying <a name="b"></a>
Colour is an incredibly useful way to help people understand your graphs, 
this is particularly true when paneling.
The whole point of paneling together plots is to help the reader have a deeper understanding of how the plots fit together,
but the connection between them can be not obviously apparent and colour coordination can help with that.

Take the boxplot panel:
```
#### Beautifying ####

bm_is|bmsp_dream/bmsp_biscoe
```

> pl4_1
<img width="720" alt="pl4_1-1" src="https://user-images.githubusercontent.com/91271747/144592107-a281811a-104a-4366-ae93-a6797d51e76e.png">

Since we know how we're panelling the plots, it's easier to figure out how to colour them to emphasise the distinctions.

We'll use a colourblind- friendly pallet of: `"#000000", "#E69F00", "#56B4E9", "#009E73", "#F0E442", "#0072B2", "#D55E00", "#CC79A7", "#999999"`
```
(col1<-ggplot(subset(penguins,penguins$species=="Adelie"),aes(x=island,y=body_mass_g, fill=island))+
  geom_boxplot(fill=c("#D55E00","#E69F00","#CC79A7"), colour="#000000")+
  xlab("Island")+
  ylab("Body Mass (g)")+
  labs(title="Adelie Body Mass on Each Island")+
    theme(panel.background = element_rect(fill="#56B4E9"),
  panel.grid.major =element_line(color = "#999999"),
  panel.grid.minor =element_line(color = "#999999")))

(col2<-ggplot(subset(penguins,penguins$island=="Dream"),aes(x=species,y=body_mass_g))+
  geom_boxplot(fill=c("#56B4E9","#009E73"), colour="#000000")+
  xlab("Species")+
  ylab("Body Mass (g)")+
  labs(title="Comparing Body Mass of Adelie and Chinstrap Penguins", 
       subtitle = "from Dream Island")+
  theme(panel.background = element_rect(fill="#E69F00"),
        panel.grid.major =element_line(color = "#999999"),
        panel.grid.minor =element_line(color = "#999999")))

(col3<-ggplot(subset(penguins,penguins$island=="Biscoe"),aes(x=species,y=body_mass_g))+
  geom_boxplot(fill=c("#56B4E9","#0072B2"))+
  xlab("Species")+
  ylab("Body Mass(g)")+
  labs(title="Comparing Body Mass of Adeline and Gentoo Penguins", 
       subtitle = "from Biscoe Island")+
    theme(panel.background = element_rect(fill="#D55E00"),
          panel.grid.major =element_line(color = "#999999"),
          panel.grid.minor =element_line(color = "#999999")))
   ```
 Which we can then panel:
 ```
 col1|col3/col2
 ```
 
 > pl4_2
 <img width="720" alt="pl4_2" src="https://user-images.githubusercontent.com/91271747/144592159-edb33ec6-b845-44a9-8399-128f1de584cf.png">

The colours were used like this because the penguine species on all 3 islands- Adelie- never shared an island with more than one other species.
The background colour of the only Adelie plot was used as the fill for the Adelie box on the other two plots. The colour used as a background for the other two plots
is the same as the colour used for the island they share with the Adelie penguins. Therefore, the colours make a throughline between the graohs without any possible legends conflicting.

This helps explain to the reader how the graphs are connected with trying to get them to read a paragraph about it. Very handy!

:penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin::penguin:

__Hints__

For coherent themes across all plots in the panel, use `&` followed by [your chosen theme](https://ggplot2.tidyverse.org/reference/ggtheme.html)

To add a theme to one object in particular, use `*` instead.


# 6. Challenge <a name="c"></a>

Paneling well is about aesthetic and what's more aesthetically pleasing than a fibonacci spiral? See if you can compose some plots into the dimensions and shape of a spiral like the one below (if it doesn't show up, just google 'fibonacci spiral' and go on images)
> Make a fibanacci spiral from graphs
  ![image](https://user-images.githubusercontent.com/91271747/144523196-ca818aae-88c8-45f6-916d-ebf32138b457.png)


# References
Horst AM, Hill AP, Gorman KB (2020). palmerpenguins: Palmer. Archipelago (Antarctica) penguin data. R package version 0.1.0. https://allisonhorst.github.io/palmerpenguins/. doi:10.5281/zenodo.3960218.
Pedersen, T. L. (2021). Patchwork [online]. Available at: https://patchwork.data-imaginist.com/index.html [accessed: 01/12/2021].
