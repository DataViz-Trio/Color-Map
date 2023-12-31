# Color Mapping

## Dataset Description:

- The color mapping dataset follows the following pattern:
- The First Six lines of the dataset provide some information about the dataset such as the variable being plotted (surface rain rate in our case), the Flag which denotes a bad value (-999 in this dataset), the number of rows and columns in the dataset and the date on which the data was collected.
- From the 7th line, the data is presented. The dataset follows the following pattern:
- Each column corresponds to a longitude. The longitudes begin at 0.125E.
- Each row corresponds to a latitude. The latitudes begin at 89.875S and ends at 89.875N.
- Each cell corresponding to a column (longitude) and a row (latitude) contains the sea surface rain rate at that longitude and latitude.
- Our group had chosen April - May 2016 as the months for performing the Color Mappings. Hence, we chose 10 dates: 
    - 1, 10, 20, 30 April 2016
    - 10, 20, 30 April 2016
    - 10, 20, 30 June 2016 

## Data Processing: 

- We ignore the first 7 lines and start reading the text file from the 8th line. We ignore the 7th line as well since the longitudes in the dataset are in the same order that the library expects. Hence, we do not need to explicitly store the longitudes to perform any sort of data processing later on.
- We store each line in a list. We drop the first element of each list, which corresponds to the latitude for that line. The reason for dropping the latitude is the same as the reason for dropping the longitudes. 
- Due to the limitation of ASCII Text files, certain rows contain the message ** line too long ** at the end of the line.
- We split the line at all the spaces. If the last element of the line contains ** line too long **, we pop the last element out of the list. We then convert all strings (the surface rain rates) to their float values. 
- Due to the limitation of Text files, not all rows contain 1440 values. Hence, we keep adding the BAD VALUE to the list until the length of the list is 1440. 
- All these lists (rows) are added to another list to generate a 2D list.

## Data Analysis

- In the *data_analysis.ipynb* file, we analyse the data set
- We first check the maximum value for all the different dates. We notice that it is 25 for 9 out of the 10 dates and 16.78 for only one date.
- Hence, we decide to keep the maximum value in the boundaries of the discrete color mapping as 25.
- We next analyse the boundaries that will be assumed for the discrete color mapping.
- We check the percentage of values which are greater than 2 for each dataset and notice that only 1% of values in each dataset are greater than 2.
- Hence, in the color mapping, all values greater than 2 will be colored with an "outlier color" which is different from the colors used in the color mapping for the other points. For eg. red for the viridis color map.

## Color Mapping Implementation

- The *generate_dataset(name)* is a function that takes in a name of the text file (dataset), processes it and returns a 2D List, as described in the Data Processing section.

- The *continuous_plot(data,title,cmap)* performs a continuous color mapping of the data on the world map. The implementation of the method is as follows:

    - Input Parameters:
        - data: A 2D NumPy array or similar data structure containing the values to be plotted.
        - title: A string specifying the title of the plot.
        - cmap: A colormap object that determines the color mapping of the data values.

    - Plot Initialization:
        - The function starts by creating a new figure and axis using Matplotlib[^1] (fig, ax = plt.subplots(...)).
        - It specifies a Plate Carrée projection for the axis, which is commonly used for world maps, with the central longitude set to 180 degrees.

    - Plot Data: The data is plotted on the map using the imshow function with the following parameters:
        - data: The 2D array to be plotted.
        - origin='lower': Specifies that the data's origin is in the lower-left corner.
        - cmap: The colormap used for color mapping.
        - extent: The spatial extent of the data, specified as (-180, 180, -90, 90), covering the entire world.
        - transform: The transformation to Plate Carrée projection with the central longitude set to 180.
        - vmin and vmax: The minimum and maximum values for color scaling.

    - Map Features: The function adds coastlines to the map using ax.add_feature(cfeature.COASTLINE) to provide geographic context.

    - Colorbar: A colorbar is added to the plot using plt.colorbar(...). It displays the color mapping of the data with the specified title.

    - Axis Labels and Title: The function sets labels for the X and Y axes, as well as a title for the plot using ax.set_xlabel(...), ax.set_ylabel(...), and ax.set_title(...).

    - Map Extent: We set the map's extent to cover the world from 0&deg; Longitude to 300&deg; Longitude and -90&deg; Latitude to +90&deg; Latitude with ax.set_extent([0, 300, -90, 90], ccrs.PlateCarree()).

    - Display the Plot: Finally, the function displays the plot using plt.show().

- The *discrete_plot* function is similar to the previously described *continuous_plot* function, but with a focus on visualizing discrete data rather than continuous data. It plots data on a world map using Matplotlib and Cartopy, with discrete color mapping based on specified boundaries and colors. Here's what is different in the implementation of discrete mapping:

    - Boundaries and Colors:
        - The boundaries list defines the boundaries that separate the discrete intervals for data visualization.
        - The colors list defines a corresponding set of colors for each category, with a specific color assigned to each range between the boundaries. The list of colors should be in the same order as the boundaries, with colors assigned to categories from low to high values. Finally, an outlier color is added to the colors list which maps all values greater than 2 to that color. Each color map has a different outlier color for it based on the colors already present in the color axis.

    - BoundaryNorm: 
        - The BoundaryNorm is an essential component for discrete color mapping. It is created with the boundaries and the number of colors to be used (len(colors) in this case).
        - The BoundaryNorm is used to map data values to the appropriate color category based on the specified boundaries. It ensures that data falling within a specific range is assigned the corresponding color from the colors list. 

For the purpose of the discrete color mapping, discrete colors are generated for a given color mapping using the plt.get_cmap method.

- The *log_plot* function is another function for visualizing data on a world map using Matplotlib and Cartopy, but it differs from the previous functions in how it handles data scaling and color mapping. Here's a description of what is different in this function:

    - Scaling with Logarithmic Normalization: The primary difference in this function is the use of logarithmic normalization for scaling the data. This is achieved by creating a LogNorm instance and assigning it to the norm variable.

An offset of 10^-10 is added to all points to avoid white points on the plot where the value is 0 due to logarithmic normalization. 

## Implementation

- In order to try different color maps, only the first dataset (of 1st April 2016) is chosen. 
- The dataset is fed to the *data = numpy.ma.masked_where(data == BAD_VALUE, data)* masks the BAD_VALUE and is not plotted on the world map.
- Then, to decide the best color map, I followed the following steps and performed the color mapping on all the 10 dates chosen:
    - First, I performed a continous, discrete and logarithmic color mapping. Refer to the report as to why discrete color mapping was finally chosen.
    - Then, I performed a discrete plot with different color mappings[^3]. Refer to the report to view the different experimented color mappings.
- After zeroing in on discrete plot, the images for the color mappings for all 10 dates were generated.
- Finally, an .mp4 animation was created using the 8 different discrete color maps for all 10 dates.

[^1]: https://matplotlib.org/stable/ 
[^3]: https://matplotlib.org/stable/users/explain/colors/colormaps.html