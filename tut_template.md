<center><img src="{{ site.baseurl }}/tutheaderbl.png" alt="Img"></center>


### Tutorial Aims

#### <a href="#section1"> 1. Download Data</a>

#### <a href="#section2"> 2. Make density map</a>

#### <a href="#section3"> 3. Feel satisfied at a job well done</a>


# Subheading 1
## Subheading 2
### Subheading 3

This is some introductory text for your tutorial. We’re are going learn how to produce a density map, how to download GBIF data, and probably something else I’ll fill in later.

You can get all of the resources for this tutorial from <a href="https://github.com/ourcodingclub" target="_blank">this github repository</a>. Clone and download the repo as a zip file, then unzip it.

<a name="section1"></a>

## 1. Download Data


At the beginning of your tutorial open `RStudio`, create a new script by clicking on `File/ New File/ R Script` set the working directory, and load the following packages.

```r
# Set the working directory
setwd("your_filepath")

# Load packages
library(ggplot2)
library(dplyr)
library(rgbif)
library(raster)
library(sp)
library(viridis)

# Retrieve isocode for UK
UK_code <- isocodes[grep("United Kingdom", isocodes$name), "code"]

# Search for red squirrel occurrences in GBIF from 2005 to 2010
# Download only occurrences that have geographic coordinates and up to 25000 occurrences
# and return data, this takes a few minutes
occur <- occ_search(scientificName = "Sciurus vulgaris", country = UK_code, 
                  hasCoordinate = TRUE, limit = 25000, year = '2005,2010', return = "data")

# Explore data
str(occur)

# Download shapefile of UK boundaries
UK <- getData("GADM", country = "GB", level = 0)


```

<a name="section2"></a>

## 2. Make density map

Transform the data into spatial coordinates (etc.)


```r
# Make the occurrences data spatial
coordinates(occur) <- c("decimalLongitude", "decimalLatitude")

# Assign coordinate system
crs.geo <- CRS("+proj=longlat +ellps=WGS84 +datum=WGS84")  # geographical, datum WGS84
proj4string(occur) <- crs.geo                             # assign the coordinate system

# Project both spatial objects to utm
UK_proj <- spTransform(UK, CRS("+proj=utm +datum=WGS84 +no_defs +ellps=WGS84 +towgs84=0,0,0 "))
occurr_proj <- spTransform(occur, CRS("+proj=utm +datum=WGS84 +no_defs +ellps=WGS84 +towgs84=0,0,0 "))

# Transform data in ggplot2 format 
UK.Df <- fortify(UK_proj, region = "ID_0")
occurr.points <- fortify(cbind(occurr_proj@data, occurr_proj@coords))
```

Plot the occurrences in a pretty graph

```r
# Plot occurrences
(plot.occurr <- ggplot(data = occurr.points, aes(x = decimalLongitude, y = decimalLatitude)) +  # plot the data
  geom_polygon(data = UK.Df,aes(x = long, y = lat, group = group),                             # plot the UK
               color = "black", fill = "gray82") + coord_fixed() +    # coord_fixed() ensures that one unit on the x-axis is the same length as one unit on the y-axis
  geom_point(color = "black", shape = "."))               # graphical parameters for points
```

And finally, plot the density map:

```
# Plot density map
(plot.density <- plot.occurr +                   
  stat_density2d(aes(x = decimalLongitude,                           # create the density layer based on where the points are
                     y = decimalLatitude,  
                     fill = ..level.., alpha = ..level..),           # colour and transparency depend on density
                 geom = "polygon") +                                 # graphical parameters for the density layer
  scale_fill_viridis(breaks = c(0.00000000002, 0.00000000005, 0.00000000008),  # set colour palette for density layer
                     labels = c("Low", "Medium", "High")) +          # specify scale breaks and labels                                  
  scale_alpha(range = c(0.6, 1), guide = FALSE) +                    # set transparency for the density layer 
  theme(axis.title.x = element_blank(), axis.text.x = element_blank(),   # don't display x and y axes labels, titles and tickmarks 
      axis.ticks.x = element_blank(), axis.title.y = element_blank(),   
      axis.text.y = element_blank(), axis.ticks.y = element_blank(),
      text = element_text(size = 18),                                # size of text 
      panel.grid.major = element_blank(),                            # eliminates grid lines from background
      panel.background = element_blank()) +                          # set white background
  labs(fill = "Density\n"))

# Plot density maps by year
(plot.density.year <- plot.density + facet_wrap(~ year))   # multipanel plot according to the variable "year" 

# Save maps
ggsave(plot.density, filename = "density_rs.pdf", width = 10, height = 10)
ggsave(plot.density, filename = "density_rs.png", width = 10, height = 10)

ggsave(plot.density.year, filename = "density_rs_yearly.pdf", width = 15, height = 10)
ggsave(plot.density.year, filename = "density_rs_yearly.png", width = 15, height = 10)

```

At this point it would be a good idea to include an image of what the plot is meant to look like, so students can check they've done it right. Replace `IMAGE_NAME.png` with your own image file:

<center> <img src="{{ site.baseurl }}/img/density_rs.png" alt="Img" style="width: 800px;"/> </center>

<a name="section1"></a>

## 3. Feel satisfied at a job well done

This is the end of the tutoria. In this tutorial we learned:

##### - how to download GBIF data
##### - how to create a density map
##### - how to create a coding club tutorial

You can now get yourself a beer. Nice one!


<hr>
<hr>

#### Check out our <a href="https://ourcodingclub.github.io/links/" target="_blank">Useful links</a> page where you can find loads of guides and cheatsheets.

#### If you have any questions about completing this tutorial, please contact us on ourcodingclub@gmail.com

#### <a href="INSERT_SURVEY_LINK" target="_blank">We would love to hear your feedback on the tutorial, whether you did it in the classroom or online!</a>

<ul class="social-icons">
	<li>
		<h3>
			<a href="https://twitter.com/our_codingclub" target="_blank">&nbsp;Follow our coding adventures on Twitter! <i class="fa fa-twitter"></i></a>
		</h3>
	</li>
</ul>

### &nbsp;&nbsp;Subscribe to our mailing list:
<div class="container">
	<div class="block">
        <!-- subscribe form start -->
		<div class="form-group">
			<form action="https://getsimpleform.com/messages?form_api_token=de1ba2f2f947822946fb6e835437ec78" method="post">
			<div class="form-group">
				<input type='text' class="form-control" name='Email' placeholder="Email" required/>
			</div>
			<div>
                        	<button class="btn btn-default" type='submit'>Subscribe</button>
                    	</div>
                	</form>
		</div>
	</div>
</div>
